# 8.2-DevOps.-CICD
**Задание 1.**  
Устанавливаем Jenkins, go  
![изображение](https://user-images.githubusercontent.com/118304300/230894982-810c262a-0392-4065-abaa-5ac1273ae9af.png)
![изображение](https://user-images.githubusercontent.com/118304300/230895683-28f3cf48-2bfd-460a-9289-ef779886f8dd.png)  

**Задание 2.**  
Декларативный вид:
```
pipeline {
 agent any
 stages {
  stage('Git') {
   steps {git 'https://github.com/Copakaban/sdvps-materials.git'}
  }
  stage('Test') {
   steps {
    sh '/usr/local/go/bin/go test .'
   }
  }
  stage('Build') {
   steps {
    sh 'docker build . -t ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER'
   }
  }
 }
}
```
![изображение](https://user-images.githubusercontent.com/118304300/230896347-7508d79c-c21f-47e1-a3f3-100fd4f73a9d.png)  

**Задание 3.**  
Запускаем Nexus: `docker run -d -p 10.33.48.10:8081:8081 -p 10.33.48.10:8082:8082 --name nexus -e INSTALL4J_ADD_VM_PARAMS="-Xms512m -Xmx512m -XX:MaxDirectMemorySize=273m" sonatype/nexus3`  
Выводим пароль администратора для первого входа в Nexus, далее попросит его поменять: `docker exec -t nexus bash -c 'cat /nexus-data/admin.password && echo'`  
Далее заходим, логинимся и создаем репозиторий:
![изображение](https://user-images.githubusercontent.com/118304300/230870669-1ca8e5bb-4456-4d91-b5db-4bca581511ff.png)  
URL репозитория пригодится при создании pipeline.  
Создаем новый pipeline:  
```
pipeline {
 agent any
 stages {
  stage('Git') {
   steps {git 'https://github.com/Copakaban/sdvps-materials.git'}
  }
  stage('Test') {
   steps {
    sh '/usr/local/go/bin/go test .'
   }
  }
  stage('Build') {
   steps {
    sh '/usr/local/go/bin/go build -a -installsuffix nocgo'
   }
  }
  stage('Push') {
   steps {
    sh 'curl -u admin:admin http://10.33.48.10:8081/repository/raw-hosted/ --upload-file ./sdvps-materials' 
   }
  }
 }
}
```
![изображение](https://user-images.githubusercontent.com/118304300/230872175-2de3cef1-7c9f-4365-8015-862053389b31.png)
![изображение](https://user-images.githubusercontent.com/118304300/230872520-72c03e19-fd9b-4217-a976-ad72038beaa8.png)  

**Первые два задания пытался сделать полностью, так как представлен код в материалах, но никак не хотели выполняться последние шаги.**
