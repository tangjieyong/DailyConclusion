使用mybatis中的代理开发要确保接口和mapper.xml文件同名并且在同一个包下，但是Maven中默认不加载Java文件夹下的xml和properties文件因此要修改pom.xml让maven支持Java包下的配置文件扫描

```xml
 </pluginManagement>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
    </resources>
</build>
```

