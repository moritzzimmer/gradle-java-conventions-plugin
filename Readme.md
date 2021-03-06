Gradle Java Conventions Plugin ![Build Status](https://snap-ci.com/WeltN24/gradle-java-conventions-plugin/branch/master/build_image)
==============================

This plugin applies best practices and conventions for developing Java based mircoservices at [welt](https://github.com/WeltN24).

## Usage

Build script snippet for use in all Gradle versions:

    buildscript {
      repositories {
        maven {
          url "https://plugins.gradle.org/m2/"
        }
      }
      dependencies {
        classpath "gradle.plugin.de.weltn24:java-conventions:2.0.0"
      }
    }
    
    apply plugin: "de.weltn24.java-conventions"
    
Build script snippet for new, incubating, plugin mechanism introduced in Gradle 2.1:

    plugins {
      id "de.weltn24.java-conventions" version "2.0.0"
    }


## Preconditions
- currently this plugin supports only Java projects using JDK 8 and git
- plugin tested with [Gradle](http://gradle.org/) 2.4+ in single and multi project setups
- plugin tested with [IntelliJ IDEA](https://www.jetbrains.com/idea/) 14.1+

## Conventions

### Project

The following standard Gradle plugins will be applied automatically:

+ [java](https://docs.gradle.org/current/userguide/java_plugin.html)
+ [idea](https://docs.gradle.org/current/userguide/idea_plugin.html)
+ [eclipse](https://docs.gradle.org/current/userguide/eclipse_plugin.html)
+ [jacoco](https://docs.gradle.org/current/userguide/jacoco_plugin.html)

Furthermore  the [jcenter](https://bintray.com/bintray/jcenter) repository is configured for dependency resolution.

### Java 

All compile tasks are configured to use **UTF-8** encoding and [incremental](https://docs.gradle.org/current/userguide/java_plugin.html) compilation.
    
### Tests

#### Pyramid

Inspired by Martin Fowlers [proposal](http://martinfowler.com/articles/microservice-testing/) for combining testing strategies to get a high coverage, custom source sets, configurations and tasks are configured in addition to the build-in unit test setup:

1. Integration tests: *An integration test verifies the communication paths and interactions between components to detect interface defects.*
2. Component tests: *A component test limits the scope of the exercised software to a portion of the system under test, manipulating the system through internal code interfaces and using test doubles to isolate the code under test from other components.*
3. Smoke tests: Used during blue/green deployment for quickly checking the health of the new service version before switching.

Using those configurations it's possible to configure distinct dependencies for the different test strategies. Example:
    
    integrationTestCompile(
      'com.jayway.jsonpath:json-path:2.0.0'
    )
    smokeTestCompile(
      'com.jayway.restassured:rest-assured:2.4.1'
    )

| Strategy | Configurations | Task | Source Set |
| ---- | ---- | ------------- | ------------- |
|Integration tests| integrationTestCompile, integrationTestRuntime| integrationTest| integrationTest |
|Component tests| componentTestCompile, componentTestRuntime| componentTest| componentTest |
|Smoke tests| smokeTestCompile, smokeTestRuntime| smokeTest| smokeTest |

All custom configurations extend the test scope and have access to the production code. Furthermore integration and component tests are configured to be executed during *check* task of the Java plugin. Run *gradlew tasks* for more information.

#### Dependencies

[AssertJ](http://joel-costigliola.github.io/assertj/assertj-core-quick-start.html) is added to the test compile scope for fluent assertions.

It's possible to overwrite the default version in the target project.

| Type | Name | Default Value | Description |
| ---- | ---- | ------------- | ----------- |
|String| assertjVersion| "3.1.0"| sets a specified version of assertj in your project |

Example:

    project.weltn24JavaConventions {
        assertjVersion = '3.0.0'
    }

#### Parallel unit test execution

It's possible to enable parallel execution of unit tests    

| Type | Name | Default Value | Description |
| ---- | ---- | ------------- | ----------- |
|bool| runTestsParallel| false| On/Off switch for parallel unit testing |
|int| testThreads | Runtime.runtime.availableProcessors()/2 | number of workers for test runs |

Example:

    project.weltn24JavaConventions {
        runTestsParallel = true
        testThreads = 4
    }

### IDEA

Language level and JDK will be configured to *Java 1.8* the VCS is set to *git*.

## Publishing

Publishing is automatically done by snap-ci after a commit with increased version.
    
## Copyright (c) 2015 WeltN24 GmbH

Released under the [MIT license](https://tldrlegal.com/license/mit-license).
