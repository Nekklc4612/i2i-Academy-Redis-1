# i2i-Academy-Redis-1

Thia project's goal is understanding mechanism and applications  of the redis.


Firstly I pulled redis container in my local device and then connect to RedisInsight dashboard. After the connection I just started the build Java 
code to download my person datas into the redis and pull out some of these datas successfully. 

XML file:
To connection between my redis and java files I had to use "Jedis" libraries. So that I integrated this library to the my project from XML file.

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>org.example</groupId>
        <artifactId>i2isystems-redis</artifactId>
        <version>1.0-SNAPSHOT</version>
    
        <properties>
            <maven.compiler.source>26</maven.compiler.source>
            <maven.compiler.target>26</maven.compiler.target>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        </properties>
        <dependencies>
            <dependency>
                <groupId>redis.clients</groupId>
                <artifactId>jedis</artifactId>
                <version>5.1.0</version>
            </dependency>
        </dependencies>
    </project>


Adter add the libraries, I built my Person file to determine my data and informations about each groups. Every data has name, age and id informations.

    public class Person {
        private int id;
        private String name;
        private int age;
    
        public Person(int id, String name, int age) {
            this.id = id;
            this.name = name;
            this.age = age;
        }
    
        @Override
        public String toString() {
            return "Person{" +
                    "id=" + id +
                    ", name='" + name + '\'' +
                    ", age=" + age +
                    '}';
        }
    }


At the main file I built the structure to generate a dataset the data to dataset which has 10,000 data. To transport data into to redis from docker I organized 'HashMap' and filled this with my data. At the end to see I connected redis successfully and then read data from this, I built structure to pull first 5 data.

    import redis.clients.jedis.Jedis;
    import java.util.HashMap;
    import java.util.Map;
    
    public class Main {
        public static void main(String[] args) {
    
    
            try (Jedis jedis = new Jedis("localhost", 6379)) {
                System.out.println("Connected Successfully!");
    
    
                System.out.println("10.000 data is coming...");
                for (int i = 1; i <= 10000; i++) {
    
    
                    Map<String, String> personData = new HashMap<>();
                    personData.put("id", String.valueOf(i));
                    personData.put("name", "User" + i);
                    personData.put("age", String.valueOf(20 + (i % 30)));
    
    
                    jedis.hset("person:" + i, personData);
                }
                System.out.println("Transport Finish!");
    
    
                System.out.println("\nReading from Redis...");
                for (int i = 1; i <= 10000; i++) {
                    Map<String, String> retrievedData = jedis.hgetAll("person:" + i);
    
    
                    if (i <= 5) {
                        System.out.println("The Data " + i + ": " + retrievedData);
                    }
                }
                System.out.println("Every step successfully finish.");
    
            } catch (Exception e) {
                System.err.println("Connection error: " + e.getMessage());
            }
        }
    }

