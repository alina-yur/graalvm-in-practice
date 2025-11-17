# GraalVM in Practice

**Deep Dive: GraalVM in Practice (recording)**
 <div align="center">
  <a href="https://www.youtube.com/watch?v=1J6mbMReOnI">
    <img src="https://i.ytimg.com/vi/1J6mbMReOnI/hq720.jpg">
  </a>
</div>

* GraalVM components 🐰
* Migrating an app to GraalVM 🤖 👩‍💻 [spring-petclinic](https://github.com/spring-projects/spring-petclinic)
  * 👩‍💻 use Native Build Tools 🛠️
  * 👩‍💻 basic performance measurements with `time` 📈
* Reflection and Using libraries 📚
  * Basic Use: configuration not needed or automatically provided
    * A library might be designed to be Native-Image friendly out of the box;
    * A library might add config files to its source code;
    * Library configuration can be provided via GraalVM Reachability Metadata Repository;
    * Recommended: use `-H:Preserve`
    * Recommended: Use Dynamic Access Report
  * Advanced use: programatic or semi-automatic configuration registration
    * You can programmatically generate configuration via framework annotations, such as `@ReflectiveAccess` 👩‍💻 [native-micronaut-reflection](https://github.com/alina-yur/native-micronaut-reflection)
    * You can use Native Image's Tracing Agent (`java -agentlib:native-image-agent -jar ./target/demo.jar`)
    *  You can provide configuration manually via json
  * Crema
* Performance optimizations 🚀
  * [Overview of compiler optimization levels](https://github.com/alina-yur/native-spring-boot?tab=readme-ov-file#optimization-levels-in-native-image)
  * 👩‍💻 PGO 👩‍💻 [spring-petclinic](https://github.com/spring-projects/spring-petclinic)
    * ML-enabled PGO (in `-O3`)
  * G1 GC 👩‍💻
  * `-march=native`
  * Memory management (`xmx`)
  * Demo: [spring-petclinic](https://github.com/spring-projects/spring-petclinic)
  * Advanced level 
    * Flamegraph in build reports 👩‍💻
    * You can use LCOV tooling to [visualize](https://www.graalvm.org/latest/reference-manual/native-image/optimizations-and-performance/PGO/LCOV/) PGO profiles
      * Build with `--pgo-instrument -H:+ProfilingLCOV`
      * Run with `./target/spring-petclinic-instrumented-lcov`
      * Visualize in the tooling of your choice, e.g. `genhtml`
    *   * `perf stat `./target/spring-petclinic-optimized`
* Testing 👨‍🔬
  * You can your tests in the native mode
  * Fine-grained JUnit support for flexibility: `@EnabledInNativeImage`, `@DisabledInNativeImage`
  * Specifically test in your CI/CD and when adding new dependencies
  * Look at framework modules, such as Micronaut Test Resources or Quarkus Dev Services
* Deployment 📦
  * 👩‍💻 Packaging and linking options 
      * Dynamic linking: smallest binaries, fastest builds, no special toolchain. Needs shared libs provided by the excution platform.
      * Mostly-static: statically links everything except the C standard library (libc). Fewer runtime dependencies, smaller than fully static, but still requires libc and not as portable as fully static.
      * Fully static: fully self-contained, runs on scratch, highly portable. Larger binaries, requires recompiling for both code & patch updates.
      *  Demo: 👩‍💻 [native-spring-boot](https://github.com/alina-yur/native-spring-boot) `docker images | grep "native-spring"`
  * Recipe for the smallest container images: Native | Static | From scratch | Optimize for size
  * You can scan the images for vulnerabilities & packages 🛡️
  * Buildpacks
  * GitHub actions

* Monitoring 📈
  * Micrometer, for example [Micronaut Micrometer](https://micronaut-projects.github.io/micronaut-micrometer/latest/guide/)
  * `jvmstat`
  * JFR, JMX, `jcmd`
  * Cloud vendors solutions

* GraalVM 25 🐰

    * Zero configuration migration with [`-H:Preserve=all`](https://github.com/oracle/graal/pull/10180)
     * Taking it one step further: use the new tracing agent to produce precise configuration with `-XX:TraceMetadata=path=`
  * Security 🛡️
    *  Security by design and by default
      * SBOM options: `--enable-sbom=[embed|export|classpath|class-level]`
        * 👩‍💻 Vulnerability Scanning
        * `grype`: `native-image-inspect --sbom ./target/demo-sbom | grype -v`
      * 👩‍💻 SBOM support: exposing in Spring Actuator
      * Demo: [native-spring-boot-sbom](https://github.com/alina-yur/native-spring-boot-sbom)
    * Adavanced obfuscation: `-H:AdvancedObfuscation=`
  * Tooling: Build reports 🛠️
  * Refined ML profile inference for even higher performance out of the box, on by default in `-03`
    * Enable in `-O2`: `-H:+MLProfileInferenceUseGNNModel`
  * Use ML for smaller executables: `-H:+MLCallCountProfileInference`
  * Fast PIE with `-H:+RelativeCodePointers` [native-micronaut-reflection](https://github.com/alina-yur/native-micronaut-reflection)
  * FFM and Vector API support updates
  * DX updates 🛠️
  * Embedding Python 👩‍💻 [airline-demo](https://github.com/alina-yur/airline-demo/)

* Future work 👩‍🔬
    * [Native Image Layers](https://github.com/oracle/graal/issues/7626)
    * [GenShenandoah GC in Native Image](https://github.com/orgs/oracle/projects/6/views/1?pane=issue&itemId=130712659&issue=oracle%7Cgraal%7C12237)
    * [Web Image (javac)](https://graalvm.github.io/graalvm-demos/native-image/wasm-javac/)
    * [Project Crema](https://github.com/orgs/oracle/projects/6?pane=issue&itemId=113766307&issue=oracle%7Cgraal%7C11327)
 
