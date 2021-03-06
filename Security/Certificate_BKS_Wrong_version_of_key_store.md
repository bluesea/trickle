#BKS Wrong version of key store

`keystore` `bks`

##问题现象
在Android环境使用BouncyCastle生成的keystore文件时，提示异常“Wrong version of key store”。

##原因分析
抛出异常的位置为bcprov的jar包中JDKKeyStore的engineLoad方法。该方法会读取keystore中的版本号并与jar支持的版本号对比。如果不一致，则抛出异常。<br>
详情请对比分析bcprov-jdk16-1.46.jar和bcprov-jdk16-1.47.jar中org.bouncycastle.jce.provider.JDKKeyStore类。

##问题处理
* 检查Andorid平台内置的BoucnyCastle版本。Android不同的平台支持的版本不一样，因此需要运行一下查看内置的版本。执行以下代码，该代码会返回1.46,1.47等不同结果。
``` java 
 Security.getProvider("BC").getVersion();
```
* 根据BouncyCastle版本确定支持的keystore版本。查看JDKKeyStore中STORE_VERSION常量。经过对比，bcprov-1.46及其以前的版本支持的keystore版本为1,bcprov-1.47及其以后的版本支持的keystore版本为2。

* 检查keystore文件的版本。使用DataInputStream读取keystore文件的第一个int。可能有两个结果1或者2。

* 根据Andorid平台内置BouncyCastle版本重新生成keystore文件。

##附
[JDKKeyStore_bcprov-jdk16-1.46.java](https://github.com/duanjfeng/trickle/blob/master/attaches/JDKKeyStore_bcprov-jdk16-1.46.java)
<br>
[JDKKeyStore_bcprov-jdk16-1.47.java](https://github.com/duanjfeng/trickle/blob/master/attaches/JDKKeyStore_bcprov-jdk16-1.47.java)
<br>2014-05-25
