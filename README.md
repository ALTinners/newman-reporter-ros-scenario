# newman-reporter-ros-scenario
A modified JUnit `newman` reporter designed for use with ROS and C++ GTest

## Overview
This reporter is to help produce Newman JUnit output which aims to be closer in syntax to the JUnit XML produced by GTest, and to help facilitate the generation of test reports which emulate external manipulations of a long-running stateful machine (like a robot)

The main difference between this reporter and the stock JUnit one is with what Postman elements match to which JUnit elements. 

| JUnit Tag       | Stock reporter    | ros-scenario     | ROS GTest               |
| --------------- |-------------------|------------------|-------------------------|
| `testsuites`    | Collection        | Collection       | All Tests in a ROS Test |
| `testsuite`     | Folder/Request    | Folder           | `TEST(__Suite__, Name)` |
| `testcase`      | Each `pm.test`    | Request          | `TEST(Suite, __Name__)` |
| `failure`       | `pm.test` details | Failed `pm.test` | `EXPECT*`, `ASSERT*`    |

To allow for this sort of match up, this reporter requires your collection to conform to a Collection structure. Each Request must be contained in a Folder, which is contained in the root of the Collection. Your Collection should look something like this:

```
|_____ Folder1 _____ Request11
|             |
|             |_____ Request12
|             |
|             |_____ Request13
|
|_____ Folder2 _____ Request21
              |
              |_____ Request22
              |
              |_____ Request23

```

Any requests that don't fit this structure will still be executed, but their results will not be recorded. 

Given that HTTP API manipulations in ROS are likely emulating control from a C&C server or user signals from an external application, these Folders can sensibly represent a long running external interaction process you wish to test. This was the intended use case for its use in [this CI project](https://github.com/ALTinners/ros-gitlab-ci-example). 

## Options
`--reporter-ros-scenario-export {PATH}.xml` or just `--reporter-export {PATH}.xml` will allow you to set the output destination file, same as the [original](https://github.com/postmanlabs/newman#junitxml-reporter).

## Acknowledgements
[The original JUnit reporter](https://github.com/postmanlabs/newman/blob/develop/lib/reporters/junit/index.js) which this was derived from.