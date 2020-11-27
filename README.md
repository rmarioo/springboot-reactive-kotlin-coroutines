## Reactive Spring Boot with Kotlin Coroutines

[![license](https://img.shields.io/github/license/tginsberg/springboot-reactive-kotlin-coroutines.svg)]()


### Getting The Code

```
git clone https://github.com/tginsberg/springboot-reactive-kotlin-coroutines.git
```

### Build Information

Technologies used in this project:

1. Java 11 (but 8 will work fine)
2. Spring Boot 2.3 (but 2.2.x will work fine)
3. Kotlin 1.3.72
4. Gradle 6.3

To run this project, you'll need:

1. Redis installed and ready to use on the default port  
  
For example using docker command 
```
docker run --name redis-hs --rm -p 6379:6379 -d redis
```
Stop redis on docker
```
docker rm -f -v redis-hs
```
2. A cursory understanding of reactive concepts and Spring Boot

### Running the server

```
./gradlew bootRun
```

### Endpoints

| Purpose                  | Method | URL      | Accept Header       |
|--------------------------|--------|----------|---------------------|
| Current state of counter | GET    | `/`      | `application/json`  |
| Counter event stream     | GET    | `/`      | `text/event-stream` |
| Increment counter        | PUT    | `/up`   | `application/json`  |
| Decrement counter        | PUT    | `/down` | `application/json`  |

### Using The Counter

While I have provided a test suite to prove this all works, let’s do some manual testing. For this, I’m going to use curl, the commands for which are shown in each example.

First, getting the state of the counter:

```
$ curl localhost:8080

{
  "value" : 3,
  "at" : "2020-05-24T23:07:42.292Z"
}
```

Increment:

```
$ curl -X PUT localhost:8080/up

{
  "value" : 4,
  "at" : "2020-05-24T23:09:34.966Z"
}
```
Decrement:
```
curl -X PUT localhost:8080/down

{
  "value" : 3,
  "at" : "2020-05-24T23:10:06.51Z"
}
```
Listening to the stream of updates. You’ll have to open another command line and use the increment and decrement commands to make the counter do something. There is no replay which means if we subscribe to the counter stream, we only see events published after we subscribe.

```
$ curl -H Accept:text/event-stream localhost:8080/

data:{
data:  "value" : 4,
data:  "action" : "UP",
data:  "at" : "2020-05-24T23:11:18.96Z"
data:}

data:{
data:  "value" : 3,
data:  "action" : "DOWN",
data:  "at" : "2020-05-24T23:11:19.974Z"
data:}

data:{
data:  "value" : 4,
data:  "action" : "UP",
data:  "at" : "2020-05-24T23:11:20.638Z"
data:}

data:{
data:  "value" : 5,
data:  "action" : "UP",
data:  "at" : "2020-05-24T23:11:21.228Z"
data:}
```
