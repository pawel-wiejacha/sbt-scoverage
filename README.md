sbt-scoverage
========

sbt-scoverage is a plugin for SBT that integrates the scoverage code coverage library. Find out more about [scoverage](https://github.com/scoverage/scalac-scoverage-plugin).

Join the [scoverage](http://groups.google.com/group/scala-code-coverage-tool)
google group for help, bug reports, feature requests, and general
discussion on scoverage.

[![Build Status](https://travis-ci.org/scoverage/sbt-scoverage.png?branch=master)](https://travis-ci.org/scoverage/sbt-scoverage)
[![License](http://img.shields.io/:license-Apache%202-red.svg)](http://www.apache.org/licenses/LICENSE-2.0.txt)

## How to use

Sbt-scoverage is an SBT auto plugin. This means it will only work with SBT 0.13.5 and higher.

Add the plugin to your build with the following in project/plugins.sbt:
```scala
addSbtPlugin("org.scoverage" % "sbt-scoverage" % "1.1.0")
```

Then run the your tests with coverage enabled by entering:
```
$ sbt clean coverage test
```
or if you have integration tests as well
```
$ sbt clean coverage it:test
```

After the tests have finished you should then run

```
$ sbt coverageReport
```

to generate the reports. You will find the coverage reports inside `target/scoverage-report`. There are HTML and XML reports. The XML is useful if you need to programatically use the results, or if you're writing a tool.

If you want to see a project that is already setup to use scoverage in both sbt and maven, then clone [the scoverage samples project](https://github.com/scoverage/scoverage-samples).

## Notes on upgrading to version 1.0.0

If you are upgrading from 0.99.x then you must remove the `instrumentSettings` from your build.sbt or Build.scala, as that is no longer needed.

Next, the keys have been renamed slightly. The new names begin with coverageXXX, eg coverageExcludedPackages and some have had their full name changed. You can see a full list of keys by opening the object ScoverageKeys.

## Multi project reports

By default, scoverage will generate reports for each project seperately. You can merge them into an aggregated report by invoking `sbt coverageAggregate`. Note, you must do this after all the coverage data is complete as a separate command, so you cannot do `sbt coverage test coverageAggregate` (at least until a way around this is found).

## Exclude classes and packages

You can exclude classes from being considered for coverage measurement by providing semicolon-separated list of
regular expressions.

Example:
```scala
ScoverageSbtPlugin.ScoverageKeys.coverageExcludedPackages := "<empty>;Reverse.*;.*AuthService.*;models\\.data\\..*"
```

The regular expressions are matched against the fully qualified class name, and must match the entire string to take effect.

Any matched classes will not be instrumented or included in the coverage report.

You can also mark sections of code with comments like:

```scala
  // $COVERAGE-OFF$Disabling highlighting by default until a workaround for https://issues.scala-lang.org/browse/SI-8596 is found
  ...
  // $COVERAGE-ON$
```

Any code between two such comments will not be instrumented or included in the coverage report.

## Minimum coverage

You can use the following two keys to set the minimum coverage, and if you want to fail the build if the coverage is less than the minimum.

```scala
ScoverageSbtPlugin.ScoverageKeys.coverageMinimum := 80

ScoverageSbtPlugin.ScoverageKeys.coverageFailOnMinimum := true
```

## Highlighting

If you are using Scala 2.11.1 or less, then highlighting will not work (due to this bug which was fixed in 2.11.2 https://github.com/scala/scala/pull/3799). In that case you must disable highlighting by adding the following to your build:

```scala
ScoverageSbtPlugin.ScoverageKeys.coverageHighlighting := false
```

## Failing tests

If you are running into a scenario where your tests normally pass, but fail when scoverage is enabled, 
then the most common culprit is timing issues on futures and other async operations. Scoverage does a lot of file 
writing behind the scenes in order to track which statements have been executed, and this slows down tests slighly, so try upping the timeouts by an order of magnitude.

## Parallel test execution

Due to a bug in SBT, parallel text execution is disabled. You can enable it again by 

```scala
parallelExecution in Test := true,
```

But if you run into an error like "cannot create classes.bak" note this is not an scoverage bug, but an SBT bug.

## Coveralls

If you have an open source project then you can add code coverage metrics with the excellent website http://coveralls.io. Scoverage will integrate with coveralls using the [sbt-coveralls](https://github.com/scoverage/sbt-coveralls) plugin.

## Plugin for SonarQube

If you want to visually browse statement coverage reports then use this [plugin for SonarQube](https://github.com/RadoBuransky/sonar-scoverage-plugin).
It allows you to review overall project statement coverage as well as dig deeper into sub-modules, directories and
source code files to see uncovered statements. Statement coverage measurement can become an integral part of your
team's continuous integration process and a required quality standard.

## License
```
This software is licensed under the Apache 2 license, quoted below.

Copyright 2013-2014 Stephen Samuel and contributors

Licensed under the Apache License, Version 2.0 (the "License"); you may not
use this file except in compliance with the License. You may obtain a copy of
the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
License for the specific language governing permissions and limitations under
the License.
```
