## License 简介
License即版权许可证，一般用于收费软件给付费用户提供的访问许可证明。根据应用部署位置的不同，一般可以分为以下两种情况讨论：

- 应用部署在开发者自己的云服务器上。这种情况下用户通过账号登录的形式远程访问，因此只需要在账号登录的时候校验目标账号的有效期、访问权限等信息即可。
- 应用部署在客户的内网环境。因为这种情况开发者无法控制客户的网络环境，也不能保证应用所在服务器可以访问外网，因此通常的做法是使用服务器许可文件，
在客户应用启动的时候加载证书，然后在登录或者其他关键操作的地方校验证书的有效性。

## 原理
使用私钥签名+加密生产license，再使用公钥来解密和验签。

通过Spring Boot的autoConfig机制进行加载，无需手动配置，只需要添加如下依赖即可：

```xml
     <dependency>
        <groupId>io.gitee.mcolley</groupId>
        <artifactId>swak-license-spi</artifactId>
     </dependency>
```

## 公秘钥生成
使用 Java 生成PKCS#8格式的私钥(pkcs8_private.key)、RSA私钥(rsa_private.key)和RSA公钥(rsa_public.key)，以及带有秘钥和组织信息的证书请求，你可以使用以下步骤来实现：

- 生成密钥对和证书请求：
```shell
  keytool -genkeypair -keyalg RSA -keysize 2048 -alias mykey -keystore keystore.jks -storepass yourpassword -validity 365 -dname "CN=YourName, OU=YourOrganizationalUnit, O=YourOrganization, L=YourCity, ST=YourState, C=YourCountry"
```
在上述命令中，你需要将 yourpassword 替换为 keystore 的密码，以及将 YourName、YourOrganizationalUnit 等信息替换为实际的组织信息。

- 导出私钥为PKCS#8格式：
```shell
keytool -importkeystore -srckeystore keystore.jks -destkeystore keystore.p12 -deststoretype PKCS12 -srcalias mykey -deststorepass yourpassword -destkeypass yourpassword
openssl pkcs12 -in keystore.p12 -nodes -nocerts -out private.pem
openssl pkcs8 -topk8 -inform PEM -outform DER -in private.pem -out pkcs8_private.key -nocrypt
```
这些命令将导出私钥为 PKCS#8 格式的 private.key 文件。请确保你已经安装了 OpenSSL 工具，并能够使用其命令行工具。

- 导出RSA私钥和公钥：
```shell
keytool -v -importkeystore -srckeystore keystore.jks -destkeystore keystore.p12 -deststoretype PKCS12
openssl pkcs12 -in keystore.p12 -nocerts -nodes -out rsa_private.key
openssl rsa -in rsa_private.key -pubout -out rsa_public.key
```
其中pkcs8_private.key用来生成license文件，而rsa_public.key放入项目中用来验签和校验。

## 授权文件license.lic文件生成
使用swak-license-server来生产对应的license文件

```java
/**
 * LicenseCreatorParam入参格式：
 * {
 *   "subject": "license_sub",
 *   "keyPass": "abc@123",
 *   "edition": "standard",
 *   "issuedTime": "2024-06-14 00:00:00",
 *   "expiryTime": "2024-06-29 00:00:00",
 *   "description": "license_sub",
 *   "distinguishedName": "CN=xx.CN",
 *   "licenseCheckModel": {
 *     "ipAddress": ["2001:0:2841:aa90:34fb:8e63:c5ce:e345", "192.168.153.155"],
 *     "macAddress": ["00-00-00-00-00-00-00-E0","B0-52-16-27-F5-EF"],
 *     "userAmount": "40",
 *     "key1": ""//根据需求定义
 *   }
 * }
 * privateKeysStorePath = /pkcs8_private.key  私钥路径
 */
@RequestMapping(value = "/generateLicense")
public Response<?> generateLicense(@RequestBody LicenseCreatorParam param) {
        // 这里参数从配置中读取
        String licenseFileName = System.currentTimeMillis()+".lic";
        String licensePath = System.getProperty("java.io.tmpdir")+licenseFileName;
        param.setLicensePath(licensePath);
        param.setLicenseFileName(licenseFileName);
        param.setPrivateKeyPath(privateKeysStorePath);
        LicenseCreator licenseCreator = new LicenseCreator(param);
        boolean result = licenseCreator.generateLicense();
        if (result) {
        return Response.success(param);
        } else {
        return Response.fail(BasicErrCode.SWAK_ERROR.getCode(), "授权文件生成失败！");
        }
}
```
## 项目中license授权拦截器

```java
@Slf4j
public class LicenseCheckInterceptor implements HandlerInterceptor {
private LicenseManager licenseManager;

    private LicenseVerifyCallback licenseVerifyCallback;

    @Setter
    @Getter
    private SwakMvcPatterns swakMvcPatterns;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        try {
            License license = licenseManager.verify();
            Optional.ofNullable(licenseVerifyCallback).ifPresent(call -> call.call(license));
            return true;
        } catch (LicenseValidationException e) {
            printJson(response, e.getMessage());
        } catch (Exception e) {
            printJson(response, null);
        }
        return false;
    }
```
## LicenseConfig配置
```java
public  class LicenseConfig {
static final String LICENSE_DATA_ID = "license";
static final String publicKeyPath = "/license/rsa_public.key";
    private Source licenseContentSource(LicenseConfigService licenseConfigService) {
        String licenseContent = licenseConfigService.getConfig(LICENSE_DATA_ID);
        return () -> () -> new ByteArrayInputStream(licenseContent.getBytes(Charsets.UTF_8));
    }

    private Source publicKeySource() {
        return () -> () -> Optional
                .of(new ClassPathResource(publicKeyPath).getInputStream())
                .orElseThrow(() -> new FileNotFoundException(publicKeyPath));
    }

    @Bean
    public LicenseConfigService licenseConfigService(NacosConfigManager nacosConfigManager) {
        LicenseConfigServiceImpl licenseConfigService = new LicenseConfigServiceImpl();
        licenseConfigService.setConfigService(nacosConfigManager);
        return licenseConfigService;
    }

    @Bean
    public LicenseConfig licenseConfig(LicenseConfigService licenseConfigService) {
        LicenseConfig licenseConfig = new LicenseConfig();
        licenseConfig.setStorePass(new long[]{xxx, xx, xxxx});
        licenseConfig.setSubject("xxStandard");
        licenseConfig.setEdition("standard");
        licenseConfig.setPublicKeys(publicKeySource());
        licenseConfig.setLicense(licenseContentSource(licenseConfigService));
        licenseConfig.setLicenseVerifyCallback(new LicenseCallBack());
        licenseConfig.setLicenseMvcConfig(licenseMvcConfig());
        return licenseConfig;
    }

    private SwakMvcPatterns licenseMvcConfig(){
        return new SwakMvcPatterns()
                .addPathPatterns("/**")
                .excludePathPatterns("/updated/**", "/license/**");
    }

    static class  LicenseCallBack implements  LicenseVerifyCallback {
        @Override
        public void call(License license) {
            LicenseContext.install(license);
        }
        @Override
        public void clear() {
            LicenseContext.clear();
        }
    }
}
```
##license支持nacos配置
可以从nacos中获取license来实现项目的认证和授权
```java
    private Source licenseContentSource(LicenseConfigService licenseConfigService) {
    String licenseContent = licenseConfigService.getConfig(LICENSE_DATA_ID);
    return () -> () -> new ByteArrayInputStream(licenseContent.getBytes(Charsets.UTF_8));
    }
```

## 使用介绍
- license的数据结构

可以使用  http://项目地址/license/info 来查看按照的授权信息
```json
  {
  "consumerAmount": 1,
  "consumerType": "user",
  "extra": {
    "ipAddress": ["2001:0:2841:aa90:34fb:8e63:c5ce:e345", "192.168.153.155"],
    "macAddress": ["00-00-00-00-00-00-00-E0","B0-52-16-27-F5-EF"],
    "userAmount": "40"
  },
  "holder": {
    "name": "CN=xx.CN",
    "encoded": "MBQxEjAQBgNVBAMTCUdFVEVDSTg=="
  },
  "info": "授权证书",
  "issued": "2023-04-25 16:27:41",
  "issuer": {
    "name": "CN=xx.CN",
    "encoded": "MBQxEjAQBgNVBAMTCUdFVEVDSTg=="
  },
  "notAfter": "2023-06-01 00:00:00",
  "notBefore": "2023-04-25 16:27:41",
  "subject": "standard"
}
```
- 验证扩展信息

```java

@Resource
LicenseVerifyService licenseVerifyService;
License license = licenseVerifyService.verify();
LicenseContext.validate(license);
LicenseValidationException(new DefaultMessage("当前服务器的IP没在授权范围内"));
LicenseValidationException(new DefaultMessage("当前服务器的Mac地址没在授权范围内"));

public static void validate(final License content) throws LicenseValidationException {
    LicenseCheckModel expectedCheckModel = JSON.parseObject(JSON.toJSONString(content.getExtra()), LicenseCheckModel.class);
    AbstractServerInfo serverInfo = new AbstractServerInfo() {};
    //当前服务器真实的参数信息
    List<String> ipAddressList = serverInfo.getIpAddress();
    List<String> macAddressList = serverInfo.getMacAddress();
    if (expectedCheckModel != null) {
    //校验IP地址
    if (!serverInfo.checkIpAddress(expectedCheckModel.getIpAddress(), ipAddressList)) {
        throw new LicenseValidationException(new DefaultMessage("当前服务器的IP没在授权范围内"));
    }
    //校验Mac地址
    if (!serverInfo.checkIpAddress(expectedCheckModel.getMacAddress(), macAddressList)) {
        throw new LicenseValidationException(new DefaultMessage("当前服务器的Mac地址没在授权范围内"));
    }else {
        throw new LicenseValidationException(new DefaultMessage(BasicErrCode.SWAK_LICENSE.getMsg()));
    }
}
    
```