Building Bazel using a local gcc (gcc at a path other than /usr/bin/gcc). Needed to build bazel to be able to build tensorflow.

Useful reference: https://github.com/bazelbuild/bazel/issues/629

Minimal diffs in `tools/cpp/CROSSTOOL` (only required in the `target_cpu: "local"` section):

```
[~/s/bazel] $ gdf
diff --git a/tools/cpp/CROSSTOOL b/tools/cpp/CROSSTOOL
index 6846d70..9faf2d0 100644
--- a/tools/cpp/CROSSTOOL
+++ b/tools/cpp/CROSSTOOL
@@ -86,11 +86,12 @@ toolchain {

   tool_path { name: "ar" path: "/usr/bin/ar" }
   tool_path { name: "compat-ld" path: "/usr/bin/ld" }
-  tool_path { name: "cpp" path: "/usr/bin/cpp" }
+  tool_path { name: "cpp" path: "/apps/GCC/4.9.1/bin/cpp" }
   tool_path { name: "dwp" path: "/usr/bin/dwp" }
-  tool_path { name: "gcc" path: "/usr/bin/gcc" }
+  tool_path { name: "gcc" path: "/apps/GCC/4.9.1/bin/gcc" }
   cxx_flag: "-std=c++0x"
   linker_flag: "-lstdc++"
+  linker_flag: "-Wl,-rpath,/apps/GCC/4.9.1/lib64"
   linker_flag: "-B/usr/bin/"

   # TODO(bazel-team): In theory, the path here ought to exactly match the path
@@ -100,6 +101,9 @@ toolchain {
   cxx_builtin_include_directory: "/usr/lib/gcc/"
   cxx_builtin_include_directory: "/usr/local/include"
   cxx_builtin_include_directory: "/usr/include"
+  cxx_builtin_include_directory: "/apps/GCC/4.9.1/lib/gcc/x86_64-redhat-linux/4.9.1/include"
+  cxx_builtin_include_directory: "/apps/GCC/4.9.1/lib/gcc/x86_64-redhat-linux/4.9.1/include-fixed"
+  cxx_builtin_include_directory: "/apps/GCC/4.9.1/include/c++/4.9.1"
   tool_path { name: "gcov" path: "/usr/bin/gcov" }

   # C(++) compiles invoke the compiler (as that is the one knowing where
```
