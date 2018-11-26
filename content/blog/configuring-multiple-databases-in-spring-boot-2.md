+++
title = "Configuring multiple databases in spring boot 2"
date = 2018-11-18T05:03:04+05:30
tags = ["java", "spring", "spring boot", "spring boot 2"]
categories = []
+++

<< Work in progress >>


Have to felt the need for a spring boot application to connect with multiple relational databases?
If so, Follow on to know how to get it up and running



Add the following your `application.properties`

```properties
app.datasource.first.url=jdbc:postgresql://localhost:5432/demo-db
app.datasource.first.username=dbuser
app.datasource.first.password=dbpass

app.datasource.second.url=jdbc:mysql://localhost/second
app.datasource.second.username=dbuser
app.datasource.second.password=dbpass
```



The complete source code can be found on [github](https://github.com/kishaningithub/spring-boot-2-multiple-datasources)