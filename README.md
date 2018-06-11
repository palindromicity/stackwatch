[![Build Status](https://travis-ci.org/palindromicity/stackwatch.svg?branch=master)](https://travis-ci.org/palindromicity/stackwatch)
# StackWatch 
  The `StackWatch`, provides a wrapper around the `StopWatch` for creating multiple and
  possibly nested named timings.
  While the `StopWatch` provides functionality to time the length of operations, there is no
  context or name to go with the time tracked. It is also not possible to time nested calls with
  the `StopWatch`.
  `StackWatch` provides that functionality, allowing successive calls to
  {@link StackWatch#startTiming} to track nested calls.
  At the end of a timing 'run', a visitor interface provides the ability to visit all the timing
  'nodes' and capture their output, including the level of the call if nested. The visitor is passed
  the `level` of the current node, it's path elements, and a `Timing` instance.
  Through the `Timing` the visitor can access the `StopWatch` instance for the timing.
  The `TimingNodeInternal` provide a tree structure in support of nesting.
  A `Deque` is use to track the current time node.
  The generic type `N` is used to type the `TimingNodeInternal.name`.
  The generic type `T` is used to type the `TimingNodeInternal.tags`.
  ```java
     private void outerFunction() {
       try {
         StackWatch<String,String> watch = new StackWatch<>("OuterFunction");
         watch.start();
         functionOne(watch);
         watch.stop();
         watch.visit(new StackWatch.TimingVisitor<String,String>() {
          @Override
           public void visitTiming(int level, List<String> path,  StackWatch.Timing<String,String> timing) {
             System.out.println("Visit level " + level + " timing: " + timing.getName()) + " path: " + path + " time: "
             + timing.getStopWatch().getNanoTime() + "ns");
           }
         });
       } catch (Exception e){}
     }
 
     private void functionOne(StackWatch<String,String> watch) throws Exception {
       watch.startTiming("One", "tagOne");
       functionOneOne(watch);
       watch.stopTiming();
     }
 
     private void functionOneOne(StackWatch<String,String> watch) throws Exception {
       watch.startTiming("OneOne", "tagTwo");
       functionOneTwo(watch);
       watch.stopTiming();
     }
 
     private void functionOneTwo(StackWatch<String,String> watch) throws Exception {
       watch.startTiming("OneTwo", "tagThree");
       watch.stopTiming();
     }
 ```
  The example above would result in the following output.
  
  ```bash
     Visit level 0 timing: OuterFunction path: [OuterFunction] time: 316032287ns
     Visit level 1 timing: One path: [OuterFunction, One] time: 156596831ns
     Visit level 2 timing: OneOne path: [OuterFunction, One, OneOne] time: 106207892ns
     Visit level 3 timing: OneTwo path: [OuterFunction, One, OneOne, OneTwo] time: 52851139ns
   ``` 
  `StackWatch` also has a static factory method to create and start an instance.
   
   ```java
     private void outerFunction() {
       try {
         StackWatch{<String,String> watch = StackWatch.startStackWatch("OuterFunction");
         watch.start();
         functionOne(watch);
         watch.stop();
         watch.visit(new StackWatch.TimingVisitor<String,String>() {
           @Override
           public void visitTiming(int level, List<String> path,  StackWatch.Timing<String,String> timing) {
             System.out.println("Visit level " + level + " timing: " + timing.getName()) + " path: " + path +
             " time: " + timing.getStopWatch().getNanoTime() + "ns");
           }
         });
       } catch (Exception e){}
     }
   ```
 
 
This class is not thread safe, and is meant to track timings across multiple calls on the same thread


```xml
<repositories>
  <repository>
    <id>bintray</id>
    <url>http://dl.bintray.com/palndromicity/stackwatch</url>
    <releases>
      <enabled>true</enabled>
    </releases>
    <snapshots>
      <enabled>false</enabled>
    </snapshots>
  </repository>
</repositories>
```

```xml
<dependency>
  <groupId>com.github.palindromicity</groupId>
  <artifactId>stackwatch</artifactId>
  <version>0.0.1</version>
  <type>pom</type>
</dependency>
```
