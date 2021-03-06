package(default_testonly = 1)

load("//scala:scala.bzl",
    "scala_binary",
    "scala_library",
    "scala_test",
    "scala_macro_library",
    "scala_export_to_java",
    "scala_repl",
    "scala_test_suite",
    "scala_library_suite",
    "scala_junit_test",
    "scala_specs2_junit_test")

# The examples below show how to combine Scala and Java rules.
# ScalaBinary is the Scala equivalent of JavaBinary.

java_binary(
    name = "JavaBinary",
    srcs = ["JavaBinary.java"],
    main_class = "scala.test.JavaBinary",
    deps = [":lib_import"],
)

# TODO(bazel-team): Allow java rules to depend directly on scala_library.
# For now, we have to use a java_import proxy.
java_import(
    name = "lib_import",
    # these are the outputs of the scala_library targets
    jars = [
        ":HelloLib_deploy.jar",
        "OtherLib_deploy.jar",
        "Exported_deploy.jar",
        "Runtime_deploy.jar",
    ],
    runtime_deps = [
        "OtherJavaLib",
        "@scala//:scala-library",
    ],
)

scala_export_to_java(
    name = "lib_import_2",
    exports = [":HelloLib",
               "OtherLib",
               "Exported",
               "Runtime",
              ],
    runtime_deps = ["OtherJavaLib"],
)

java_binary(
    name = "JavaBinary2",
    srcs = ["JavaBinary.java"],
    main_class = "scala.test.JavaBinary",
    deps = [":lib_import_2"],
)

scala_binary(
    name = "ScalaBinary",
    srcs = ["ScalaBinary.scala"],
    main_class = "scala.test.ScalaBinary",
    print_compile_time = True,
    deps = [":HelloLib", ":MacroTest"],
)

scala_library(
    name = "HelloLib",
    srcs = ["HelloLib.scala"],
    plugins = ["@org_psywerx_hairyfotr__linter//jar"],
    deps = [
        "OtherJavaLib",
        "OtherLib",
        "MacroTest",
        "Exported"
    ],
)

scala_repl(
    name = "HelloLibRepl",
    deps = [":HelloLib"]
)

scala_test(
    name = "HelloLibTest",
    size = "small",  # Not a macro, can pass test-specific attributes.
    srcs = ["HelloLibTest.scala"],
    deps = [
        ":HelloLib",
    ],
)

scala_test_suite(
    name = "HelloLibTestSuite",
    size = "small",  # Not a macro, can pass test-specific attributes.
    srcs = glob(["HelloLibTest*.scala"]),
    deps = [
        ":HelloLib",
    ],
)

scala_repl(
    name = "HelloLibTestRepl",
    deps = [":HelloLibTest"])

scala_library(
    name = "OtherLib",
    srcs = ["OtherLib.scala"],
    deps = ["ExportOnly"],
)

# Test of library without src
scala_library(
    name = "ExportOnly",
    exports = [ "Exported" ],
)

scala_library(
    name = "Exported",
    srcs = ["Exported.scala"],
    runtime_deps = ["Runtime"],
)

# Test of a grouped library
scala_library_suite(
    name = "SuiteSrcs",
    srcs = glob(["Data*.scala"]),
)

scala_library(
    name = "ConsumeSuite",
    srcs = ["SuiteUsage.scala"],
    deps = ["SuiteSrcs"]
)

scala_macro_library(
  name = "MacroTest",
  srcs = ["MacroTest.scala"]
)

scala_library(
    name = "Runtime",
    srcs = ["Runtime.scala"],
)

java_library(
    name = "OtherJavaLib",
    srcs = ["OtherJavaLib.java"],
)

scala_library(
    name = "ScalaLibResources",
    srcs = ["src/main/scala/scala/test/ScalaLibResources.scala"],
    resources = [
        "//test/data:some.txt",
        "//test/src/main/resources/scala/test:byes",
        "//test/src/main/resources/scala/test:hellos",
        ],
)

scala_library(
    name = "MoreScalaLibResources",
    srcs = ["src/main/scala/scala/test/MoreScalaLibResources.scala"],
    resources = [
        "//test/data:more.txt",
        "//test/data:foo.txt",
        "//test/src/main/resources/scala/test:more-hellos",
        "//test/src/main/resources/scala/test:more-byes"
        ],
    resource_strip_prefix = "test/",
)

scala_binary(
    name = "ScalaLibBinary",
    srcs = ["src/main/scala/scala/test/ScalaLibBinary.scala"],
    main_class = "scala.test.ScalaLibBinary",
    deps = ["ScalaLibResources"],
)

scala_binary(
    name = "MoreScalaLibBinary",
    srcs = ["src/main/scala/scala/test/MoreScalaLibBinary.scala"],
    main_class = "scala.test.MoreScalaLibBinary",
    deps = ["MoreScalaLibResources"],
)

scala_repl(
    name = "ScalaLibBinaryRepl",
    deps = [":ScalaLibBinary"])

scala_repl(
    name = "MoreScalaLibBinaryRepl",
    deps = [":MoreScalaLibBinary"])

scala_repl(
    name = "ReplWithSources",
    srcs = ["A.scala"])

scala_library(
  name = "jar_export",
  exports = ["@com_twitter__scalding_date//jar"]
)

#Mix java scala
scala_library(
    name = "MixJavaScalaLib",
    srcs = glob(["src/main/scala/scala/test/mix_java_scala/*.scala"]) +
           glob(["src/main/scala/scala/test/mix_java_scala/*.java"]),
    jvm_flags = ["-Xms1G", "-Xmx4G"],
)
#needed to test java sources are compiled
scala_binary(
    name = "MixJavaScalaLibBinary",
    srcs = ["src/main/scala/scala/test/MixJavaScalaLibBinary.scala"],
    main_class = "scala.test.MixJavaScalaLibBinary",
    deps = ["MixJavaScalaLib"],
)

scala_binary(
    name = "JavaOnlySources",
    srcs = ["src/main/scala/scala/test/only_java/Alpha.java"],
    main_class = "scala.test.Alpha",
)

scala_library(
    name = "JUnitCompileTimeDep",
    srcs = ["src/main/scala/scala/test/junit/support/JUnitCompileTimeDep.scala"],
)
scala_library(
    name = "JUnitRuntimeDep",
    srcs = ["src/main/scala/scala/test/junit/support/JUnitRuntimeDep.scala"],
)
scala_junit_test(
    name = "JunitTestWithDeps",
    srcs = ["src/main/scala/scala/test/junit/JunitTests.scala"],
    suffixes = ["Test"],
    size = "small",
    deps = [":JUnitCompileTimeDep"],
    runtime_deps = [":JUnitRuntimeDep"],
    jvm_flags = ["-XX:HeapDumpPath=/some/custom/path"],
)
scala_junit_test(
    name = "JunitMultipleSuffixes",
    srcs = ["src/main/scala/scala/test/junit/JunitMultipleSuffixes.scala"],
    size = "small",
    suffixes = ["E2E","IT"],
    print_discovered_classes = True
)
scala_junit_test(
    name = "JunitMultiplePrefixes",
    srcs = ["src/main/scala/scala/test/junit/JunitMultiplePrefixes.scala"],
    size = "small",
    prefixes = ["Test","OtherCustomPrefix"],
    print_discovered_classes = True
)
scala_junit_test(
    name = "JunitPrefixesAndSuffixes",
    srcs = ["src/main/scala/scala/test/junit/JunitMultipleSuffixes.scala",
            "src/main/scala/scala/test/junit/JunitMultiplePrefixes.scala"],
    size = "small",
    suffixes = ["E2E"],
    prefixes = ["Test"],
    print_discovered_classes = True
)

scala_specs2_junit_test(
    name = "Specs2Tests",
    srcs = ["src/main/scala/scala/test/junit/specs2/Specs2Tests.scala"],
    deps = [":JUnitCompileTimeDep"],
    size = "small",
    suffixes = ["Test"],
)

# Make sure scala_binary works in test environment
[sh_test(
    name = "Run" + binary,
    srcs = ["test_binary.sh"],
    args = ["$(location %s)" % binary],
    data = [":%s" % binary],
) for binary in [
    "JavaBinary",
    "JavaBinary2",
    "ScalaBinary",
    "ScalaLibBinary",
    "MoreScalaLibBinary",
    "MixJavaScalaLibBinary",
    "JavaOnlySources",
]]

# Make sure scala_binary works in genrule environment
genrule(
    name = "ScalaBinaryInGenrule",
    tools = [":ScalaBinary"],
    outs = ["scala_binary_out.txt"],
    cmd = "$(location :ScalaBinary) > $@",
)

sh_test(
    name = "TestScalaBinaryInGenrule",
    srcs = ["test_binary.sh"],
    args = ["cat $(location :ScalaBinaryInGenrule)"],
    data = [":ScalaBinaryInGenrule"],
)
