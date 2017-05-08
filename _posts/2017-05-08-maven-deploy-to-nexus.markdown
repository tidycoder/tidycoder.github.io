
layout:     post
title:      "deploy process!"
subtitle:   ""
date:       2017-03-07 12:00:00
header-img: "img/post-bg-2015.jpg"
tags:
    - 运维
---


### 部署jar 和 pom到本地服务器中：

mvn deploy:deploy-file -DgroupId=com.baijia.commons -DartifactId=dession-serializer -Dversion=1.0-SNAPSHOT -DgeneratePom=false -Dpackaging=jar -DrepositoryId=maven-snapshots -Durl=http://lkl.data:8081/repository/maven-snapshots/ -DpomFile=dession-serializer-1.0-SNAPSHOT.pom -Dfile=dession-serializer-1.0-SNAPSHOT.jar
mvn deploy:deploy-file -DgroupId=com.baijia.commons -DartifactId=java-library-dession -Dversion=1.0-SNAPSHOT -DrepositoryId=maven-snapshots -Durl=http://lkl.data:8081/repository/maven-snapshots/ -DpomFile=java-library-dession-1.0-SNAPSHOT.pom 
bj-bom-all-0.0.9.pom
mvn deploy:deploy-file -DgroupId=com.baijia.bom -DartifactId=bj-bom-all -Dversion=0.0.9 -DrepositoryId=maven-snapshots -Durl=http://lkl.data:8081/repository/maven-snapshots/ -DpomFile=bj-bom-all-0.0.9.pom -Dfile=bj-bom-all-0.0.9.pom 

mvn deploy:deploy-file -DgroupId=com.baijia.commons -DartifactId=java-library-dession -Dversion=1.0-SNAPSHOT -DgeneratePom=false -Dpackaging=jar -DrepositoryId=maven-snapshots -Durl=http://lkl.data:8081/repository/maven-snapshots/ -DpomFile=java-library-dession-1.0-SNAPSHOT.pom -Dfile=dession-integration-redis-1.0-SNAPSHOT.jar