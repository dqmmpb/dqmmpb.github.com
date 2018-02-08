---
layout: post
title:  "RSA Demo & Postman Scrpits.md"
date:   2018-02-08 17:20:00 +0800
categories: jekyll update
---

## RSA Demo & Postman Scrpits

RSA Demo没什么好说的，这里其实重点是想说一下Postman提供的Scripts脚本（包括Pre-request Scripts和Tests Scripts）

### RSA Demo

直接参看[RSA Demo](https://github.com/dqmmpb/rsa-demo)

### Postman Scrpits

Postman提供了Scripts脚本，用来在request提交前和提交后对request和response进行处理

- Pre-request Scripts: 提交前对request进行处理，详细的描述可参看[Pre-request Scripts](https://www.getpostman.com/docs/postman/scripts/pre_request_scripts)
- Tests Scripts: 结果返回后对response进行处理，主要用来进行test测试，纤细的描述可参看[Tests Scripts](https://www.getpostman.com/docs/postman/scripts/test_scripts)

本次结合RSA的Demo，使用Postman来进行接口测试。

Postman有native app版本，也有chrome的插件版。Postman的scripts运行环境实际上是提供了个sandbox，来运行js脚本的。在sandbox环境中，Postman会注入一些工具类，参看[Postman Sandbox](https://www.getpostman.com/docs/postman/scripts/postman_sandbox)

但这些工具类的使用是依赖于Sandbox环境的。

- Postman native： 提供了类似node的独立环境，无法获得window和navigator属性
- Postman chrome app： 提供了浏览器环境，可以获取window和navigator属性

上述区别导致了在引入第三方js插件时，两个环境的scripts脚本存在无法通用的可能，这个在本次RSA Demo的测试中，着实被坑了一把。

- jsencrypt： 无法在Postman native下使用，因为其向window注入了方法，并直接使用；在Postman chrome app下，通过修改tester_sandbox.html，加入引用的库和方法，可以使用。
- jsrsasign： 可以在Postman native下使用，但需要主动提供window和navigator对象，否则依然报错。

这里提供了jsrsasign在Postman native下的使用代码。

#### Postman native

话不多说，直接上代码。

注：代码同时提供了对lodash和CryptoJS的使用。
在Postman的native下，lodash需要使用require方式引入，而jquery已经不提倡使用了，至少我还没有找到合适的方式把jquery引入，因为eval方式报错了；
但在Postman的chrome app下（已经不提倡使用插件版本的Postman了），lodash以`_`方式提供，已在window对象下，无需手动引入。而和jquery也`$`的方式在window对象下存在，也无需手动引入。

***Pre-request Scripts***
```javascript

var md5Password = CryptoJS.MD5("123").toString().toUpperCase();
console.log(md5Password);
var te = [1, 2, 3, 4];

var _ = require('lodash');
var de = _.reverse(te);
console.log(de);

window = {};
navigator = {};

pm.sendRequest({
    url: 'http://localhost:8080/rsa/rest/api/v1/init',
    method: "POST",
}, function (err, res) {
    console.log(res.json());
    console.log('-------------------- 华丽的分割线 请求前 -----------------------');
    pm.sendRequest({
        url: 'https://cdn.bootcss.com/jsrsasign/8.0.5/jsrsasign-all-min.js',
        method: "GET",
    }, function (err, res) {
        var CLIENT_PUBLICKEY = "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCPrkLZe1AxVnhckFoF/c5BbuW86/LQE5hSynrGq2Dho9SaGqEu8QpZfhqk+w6OQaM8cdiAbakty7sjRzJ47JlGzoxHlurYKfxvo1T/3N2gXFa4H0ZpZXlG+uetyTMl06ndFl9Ji9GvxVzWW2B/RRB5tsEEkdoET3AG4V5bh1VgrQIDAQAB";
        var CLIENT_PRIVATEKEY = "MIICdwIBADANBgkqhkiG9w0BAQEFAASCAmEwggJdAgEAAoGBAI+uQtl7UDFWeFyQWgX9zkFu5bzr8tATmFLKesarYOGj1JoaoS7xCll+GqT7Do5Bozxx2IBtqS3LuyNHMnjsmUbOjEeW6tgp/G+jVP/c3aBcVrgfRmlleUb6563JMyXTqd0WX0mL0a/FXNZbYH9FEHm2wQSR2gRPcAbhXluHVWCtAgMBAAECgYBBfyukHk1xIDzf3UHcZ1WFiHsbwuc+KSCP5RNQy0DvuxIoaak+T8zq/MxCltuMx6kU3cTWzqaHZM7bBxKgAyLfamrTcFyh4rUrkMzcBEENFkRng7//Px7vzwcUocygA4KGh6eGkef6/33yhgF4wUofUWgW2qyDNQm24OitLti7oQJBAPjOijg1gl6ytRrwdqPq+S4t7Y3ZHFjiis+7yhgZMZoS2JFeVtaQbbPfXzQ4eMExGEH2/lbOuMrsrEkxRUUNGfUCQQCT1apsFYDZwIcGSQ6DStEj4X+ukw5h/q2VnfI8B2u4EqbBbpzZ5DL7lo4yDDH85xifmLl0P3SrhSDeOSa/9ODZAkEA155OZHXi3GRs1MLNXjK07VM6CnK7wT/aYjpw4j97H/XzHs+t29Zga8BJhjzmUS5Vwlzlf584wAspJ2j+id/XvQJARDWLYj8xqkaYhh/jIFS+1k1O+h9DvZciRCwR/fx2iQGiCxGcMTSHCWnXxeO2lLeTtt9ige5dSF4uYhoAdQTpUQJBANoP3JGufyUusSp7UiRX/kUg1K2rfkrzg5MLNpPuKljs3mI/xGopddBogFNomZCbssGpUM+VFvNwnhZQByx58O4=";
        var SERVER_PUBLICKEY = "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCsrxY7zwnU8/u9a5KClEjEHDiGyMXYBJUr+il5Pw9KsVqFuhydjV7EiMBTykQ4XuZdaBWqlA7KMOio9xsL8nuMZOSu3fsmuSvMjt6gX/7kd3TQO2Tbjs8sFokJ3LPYqrhWhHCxFe52USMiKEanKbSZch1uPF1+pQHfV4zo8Mu6FQIDAQAB";
        var SERVER_PRIVATEKEY = "MIICdgIBADANBgkqhkiG9w0BAQEFAASCAmAwggJcAgEAAoGBAKyvFjvPCdTz+71rkoKUSMQcOIbIxdgElSv6KXk/D0qxWoW6HJ2NXsSIwFPKRDhe5l1oFaqUDsow6Kj3Gwvye4xk5K7d+ya5K8yO3qBf/uR3dNA7ZNuOzywWiQncs9iquFaEcLEV7nZRIyIoRqcptJlyHW48XX6lAd9XjOjwy7oVAgMBAAECgYEAk2mb506kq//j5R3RolsHizI0Jwt5qSCwXyxc/z4PxcmE5yerievG/Kto056VgjGxIgfahxWBUqVR1/uqQRas1A2j5/de8Y+LcpNrEuwF8YgOWmK3EAty0pgHQ1ezYSaxJ2AMBF427UrzMpGrB77UEzGE07GxbbC/sK/u66h0A/kCQQD89Q3OmWV8Gxie8XkWHeiUhseo3kZ9AYy7tRpsEkTkkWZAK2znphdHl35yDk0Cqu4uCe3usz6TfRlWu+3WK5k3AkEArsLXtUUt1IVeM0Z0Oxz8AWMb4v1lJiS4BhotZs7fyZ6DnMd+LIdfqQCLl9j3hCzdxEqIqmcuL2uGy1OYdfz9EwJAF+lGM9hWOoQJMMUcsBWFrbyL1Q+l1B04Y2n8JGkZsA16f+ha9A7ENpVAc6Gcb/seZqWzoxO4f5KcuZEsK0mVwwJAIp4qCJhZib2ZeWK9Z3BIYyX0wjQbs0CWy26oC7NzFQc3XvkNf1iZlGqtPDkYXrBchaOWCttBhNcx7ljy3HxuzQJAQxcxqCOUmLJah+Mtjb+aJQ2L6Lg3mBA62WNGxXDzpX2pAcJVZ7bNcsBq41rOpQEtQ8bEyj/Nfxxsxy/F57xuCQ==";
    
        var jsrsasign = res.stream.toString();
        // 执行jsrsasig，加载第三方库
        eval(jsrsasign);
        
        // RSAKey的扩展函数
        RSAKey.prototype.getPEM = function(key, pemHeader) {
          return hextopem(b64nltohex(key), pemHeader);
        };

        RSAKey.prototype.encryptB64 = function (text) {
          return hextob64(this.encrypt(text));
        };
        
        RSAKey.prototype.decryptB64 = function (text) {
          return this.decrypt(b64tohex(text));
        };
        
        RSAKey.prototype.getPrivateBaseKey = function () {
          var keyPem = KEYUTIL.getPEM(this, "PKCS8PRV");
          return pemtohex(keyPem);
        };
        
        RSAKey.prototype.getPrivateBaseKeyB64 = function () {
          return hextob64(this.getPrivateBaseKey());
        };
        
        RSAKey.prototype.getPublicBaseKey = function () {
          var keyPem = KEYUTIL.getPEM(this, "PKCS8PUB");
          return pemtohex(keyPem);
        };
        
        RSAKey.prototype.getPublicBaseKeyB64 = function () {
          return hextob64(this.getPublicBaseKey());
        };
        
        RSAKey.prototype.readPKCS8PrvKeyFromB64 = function (prvKeyB64) {
          var prvKeyPem = this.getPEM(prvKeyB64, "PRIVATE KEY");
          this.readPKCS8PrvKeyHex(pemtohex(prvKeyPem));
        };
        
        RSAKey.prototype.readPKCS8PubKeyFromB64 = function (pubKeyB64) {
          var pubKeyPem = this.getPEM(pubKeyB64, "PUBLIC KEY");
          this.readPKCS8PubKeyHex(pemtohex(pubKeyPem));
        };
        
        RSAKey.readPKCS9KeypairFromB64 = function(prvKeyB64, pubKeyB64) {
          var prvKey = new RSAKey();
          prvKey.readPKCS8PrvKeyFromB64(prvKeyB64);
          var pubKey = new RSAKey();
          pubKey.readPKCS8PubKeyFromB64(pubKeyB64);
        
          var result = {};
          result.prvKeyObj = prvKey;
          result.pubKeyObj = pubKey;
          return result;
        };
        
        /**
        * 加密
        * @param text 待加密的字符串
        * @param key 加密的key
        * @param isPub 是否使用publicKey加密，默认false
        * @returns {*}
        */
        function encrypt(text, key, isPub) {
            // Encrypt with key...
            var rsa = new RSAKey();
            if(isPub) {
              rsa.readPKCS8PubKeyFromB64(key);
            } else {
              rsa.readPKCS8PrvKeyFromB64(key);
            }
            var encrypted = rsa.encryptB64(text);
            
            return encrypted;
        }
        
        /**
        * 解密
        * @param text 待解密的字符串
        * @param key 解密的key
        * @param isPub 是否使用publicKey解密，默认false
        * @returns {*}
        */
        function decrypt(text, key, isPub) {
            // Decrypt with key...
            var rsa = new RSAKey();
            if(isPub) {
              rsa.readPKCS8PubKeyFromB64(key);
            } else {
              rsa.readPKCS8PrvKeyFromB64(key);
            }
            var decrypted = rsa.decryptB64(text);
            
            return decrypted;
        }

        var sPub = new RSAKey();
        sPub.readPKCS8PubKeyFromB64(SERVER_PUBLICKEY);
        
        var message = 'Java中文';
        
        var encryptMessage = encrypt(message, sPub.getPublicBaseKeyB64(), true);
        
        console.log('密文：', encryptMessage);
        
        postman.setGlobalVariable('encryptMessage', encryptMessage);
        postman.setGlobalVariable('cPub', CLIENT_PUBLICKEY);
        
    });  
    
});
```

***Tests Scripts***
```javascript
window = {};
navigator = {};

pm.test("response is ok", function () {
    console.log('-------------------- 华丽的分割线 请求后 -----------------------');
    pm.response.to.have.status(200);
    pm.sendRequest({
        url: 'https://cdn.bootcss.com/jsrsasign/8.0.5/jsrsasign-all-min.js',
        method: "GET",
    }, function (err, res) {
        var CLIENT_PUBLICKEY = "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCPrkLZe1AxVnhckFoF/c5BbuW86/LQE5hSynrGq2Dho9SaGqEu8QpZfhqk+w6OQaM8cdiAbakty7sjRzJ47JlGzoxHlurYKfxvo1T/3N2gXFa4H0ZpZXlG+uetyTMl06ndFl9Ji9GvxVzWW2B/RRB5tsEEkdoET3AG4V5bh1VgrQIDAQAB";
        var CLIENT_PRIVATEKEY = "MIICdwIBADANBgkqhkiG9w0BAQEFAASCAmEwggJdAgEAAoGBAI+uQtl7UDFWeFyQWgX9zkFu5bzr8tATmFLKesarYOGj1JoaoS7xCll+GqT7Do5Bozxx2IBtqS3LuyNHMnjsmUbOjEeW6tgp/G+jVP/c3aBcVrgfRmlleUb6563JMyXTqd0WX0mL0a/FXNZbYH9FEHm2wQSR2gRPcAbhXluHVWCtAgMBAAECgYBBfyukHk1xIDzf3UHcZ1WFiHsbwuc+KSCP5RNQy0DvuxIoaak+T8zq/MxCltuMx6kU3cTWzqaHZM7bBxKgAyLfamrTcFyh4rUrkMzcBEENFkRng7//Px7vzwcUocygA4KGh6eGkef6/33yhgF4wUofUWgW2qyDNQm24OitLti7oQJBAPjOijg1gl6ytRrwdqPq+S4t7Y3ZHFjiis+7yhgZMZoS2JFeVtaQbbPfXzQ4eMExGEH2/lbOuMrsrEkxRUUNGfUCQQCT1apsFYDZwIcGSQ6DStEj4X+ukw5h/q2VnfI8B2u4EqbBbpzZ5DL7lo4yDDH85xifmLl0P3SrhSDeOSa/9ODZAkEA155OZHXi3GRs1MLNXjK07VM6CnK7wT/aYjpw4j97H/XzHs+t29Zga8BJhjzmUS5Vwlzlf584wAspJ2j+id/XvQJARDWLYj8xqkaYhh/jIFS+1k1O+h9DvZciRCwR/fx2iQGiCxGcMTSHCWnXxeO2lLeTtt9ige5dSF4uYhoAdQTpUQJBANoP3JGufyUusSp7UiRX/kUg1K2rfkrzg5MLNpPuKljs3mI/xGopddBogFNomZCbssGpUM+VFvNwnhZQByx58O4=";
        var SERVER_PUBLICKEY = "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCsrxY7zwnU8/u9a5KClEjEHDiGyMXYBJUr+il5Pw9KsVqFuhydjV7EiMBTykQ4XuZdaBWqlA7KMOio9xsL8nuMZOSu3fsmuSvMjt6gX/7kd3TQO2Tbjs8sFokJ3LPYqrhWhHCxFe52USMiKEanKbSZch1uPF1+pQHfV4zo8Mu6FQIDAQAB";
        var SERVER_PRIVATEKEY = "MIICdgIBADANBgkqhkiG9w0BAQEFAASCAmAwggJcAgEAAoGBAKyvFjvPCdTz+71rkoKUSMQcOIbIxdgElSv6KXk/D0qxWoW6HJ2NXsSIwFPKRDhe5l1oFaqUDsow6Kj3Gwvye4xk5K7d+ya5K8yO3qBf/uR3dNA7ZNuOzywWiQncs9iquFaEcLEV7nZRIyIoRqcptJlyHW48XX6lAd9XjOjwy7oVAgMBAAECgYEAk2mb506kq//j5R3RolsHizI0Jwt5qSCwXyxc/z4PxcmE5yerievG/Kto056VgjGxIgfahxWBUqVR1/uqQRas1A2j5/de8Y+LcpNrEuwF8YgOWmK3EAty0pgHQ1ezYSaxJ2AMBF427UrzMpGrB77UEzGE07GxbbC/sK/u66h0A/kCQQD89Q3OmWV8Gxie8XkWHeiUhseo3kZ9AYy7tRpsEkTkkWZAK2znphdHl35yDk0Cqu4uCe3usz6TfRlWu+3WK5k3AkEArsLXtUUt1IVeM0Z0Oxz8AWMb4v1lJiS4BhotZs7fyZ6DnMd+LIdfqQCLl9j3hCzdxEqIqmcuL2uGy1OYdfz9EwJAF+lGM9hWOoQJMMUcsBWFrbyL1Q+l1B04Y2n8JGkZsA16f+ha9A7ENpVAc6Gcb/seZqWzoxO4f5KcuZEsK0mVwwJAIp4qCJhZib2ZeWK9Z3BIYyX0wjQbs0CWy26oC7NzFQc3XvkNf1iZlGqtPDkYXrBchaOWCttBhNcx7ljy3HxuzQJAQxcxqCOUmLJah+Mtjb+aJQ2L6Lg3mBA62WNGxXDzpX2pAcJVZ7bNcsBq41rOpQEtQ8bEyj/Nfxxsxy/F57xuCQ==";
    
        var jsrsasign = res.stream.toString();
        // 执行jsrsasig，加载第三方库
        eval(jsrsasign);
        
        // RSAKey的扩展函数
        RSAKey.prototype.getPEM = function(key, pemHeader) {
          return hextopem(b64nltohex(key), pemHeader);
        };
        
        RSAKey.prototype.encryptB64 = function (text) {
          return hextob64(this.encrypt(text));
        };
        
        RSAKey.prototype.decryptB64 = function (text) {
          return this.decrypt(b64tohex(text));
        };
        
        RSAKey.prototype.getPrivateBaseKey = function () {
          var keyPem = KEYUTIL.getPEM(this, "PKCS8PRV");
          return pemtohex(keyPem);
        };
        
        RSAKey.prototype.getPrivateBaseKeyB64 = function () {
          return hextob64(this.getPrivateBaseKey());
        };
        
        RSAKey.prototype.getPublicBaseKey = function () {
          var keyPem = KEYUTIL.getPEM(this, "PKCS8PUB");
          return pemtohex(keyPem);
        };
        
        RSAKey.prototype.getPublicBaseKeyB64 = function () {
          return hextob64(this.getPublicBaseKey());
        };
        
        RSAKey.prototype.readPKCS8PrvKeyFromB64 = function (prvKeyB64) {
          var prvKeyPem = this.getPEM(prvKeyB64, "PRIVATE KEY");
          this.readPKCS8PrvKeyHex(pemtohex(prvKeyPem));
        };
        
        RSAKey.prototype.readPKCS8PubKeyFromB64 = function (pubKeyB64) {
          var pubKeyPem = this.getPEM(pubKeyB64, "PUBLIC KEY");
          this.readPKCS8PubKeyHex(pemtohex(pubKeyPem));
        };
        
        RSAKey.readPKCS9KeypairFromB64 = function(prvKeyB64, pubKeyB64) {
          var prvKey = new RSAKey();
          prvKey.readPKCS8PrvKeyFromB64(prvKeyB64);
          var pubKey = new RSAKey();
          pubKey.readPKCS8PubKeyFromB64(pubKeyB64);
        
          var result = {};
          result.prvKeyObj = prvKey;
          result.pubKeyObj = pubKey;
          return result;
        };
        
        /**
        * 加密
        * @param text 待加密的字符串
        * @param key 加密的key
        * @param isPub 是否使用publicKey加密，默认false
        * @returns {*}
        */
        function encrypt(text, key, isPub) {
            // Encrypt with key...
            var rsa = new RSAKey();
            if(isPub) {
              rsa.readPKCS8PubKeyFromB64(key);
            } else {
              rsa.readPKCS8PrvKeyFromB64(key);
            }
            var encrypted = rsa.encryptB64(text);
            
            return encrypted;
        }
        
        /**
        * 解密
        * @param text 待解密的字符串
        * @param key 解密的key
        * @param isPub 是否使用publicKey解密，默认false
        * @returns {*}
        */
        function decrypt(text, key, isPub) {
            // Decrypt with key...
            var rsa = new RSAKey();
            if(isPub) {
              rsa.readPKCS8PubKeyFromB64(key);
            } else {
              rsa.readPKCS8PrvKeyFromB64(key);
            }
            var decrypted = rsa.decryptB64(text);
            
            return decrypted;
        }
       
        var cPrv = new RSAKey();
        cPrv.readPKCS8PrvKeyFromB64(CLIENT_PRIVATEKEY);
        
        var message = pm.response.json().result.message;
        console.log('密文：', message);
        
        var decryptMessage = decrypt(message, cPrv.getPrivateBaseKeyB64());
        
        console.log('解密后：', decryptMessage);
        
        var expected = '服务端处理成功，客户端发送过来的内容为：Java中文';
        
        pm.expect(decryptMessage).to.equal(expected);
        
    });  
});
```


`http://localhost:8080/rsa/rest/api/v1/send `接口的参数以`application/x-www-form-urlencoded`方式提供
```
message:{{encryptMessage}}
cPub:{{cPub}}
```

message是加密后的消息，
cPub是clinet端的公钥（server接收到client的请求后，将使用cPub加密返回的消息，clinet接收到response后，使用cPriv解密消息）


#### 待完成

上述两个js的插件，并没有很好的支持postman中测试，还需要进一步学习和完善。