# GraalVM in Practice

* GraalVM components 🐰
* Migrating an app to GraalVM 🤖
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
    * You can programmatically generate configuration via framework annotations, such as `@ReflectiveAccess` ([Micronaut](https://guides.micronaut.io/latest/micronaut-graalvm-reflection-maven-java.html))
    * You can use Native Image's Tracing Agent (`java -agentlib:native-image-agent -jar ./target/demo.jar`)
    *  You can provide configuration manually via json
  * Crema
* Performance optimizations 🚀
  * [Overview of compiler optimization levels](https://github.com/alina-yur/native-spring-boot?tab=readme-ov-file#optimization-levels-in-native-image)
  * 👩‍💻 PGO
    * ML-enabled PGO
    * You get [LCOV](https://www.graalvm.org/latest/reference-manual/native-image/optimizations-and-performance/PGO/LCOV/) info as a side product of PGO
  * G1 GC
  * `-march=native`
  * Memory management (`xmx`)
  * Demo: [spring-petclinic](https://github.com/spring-projects/spring-petclinic)
* Testing 👨‍🔬
  * You can your tests in the native mode
  * Fine-grained JUnit support for flexibility: `@EnabledInNativeImage`, `@DisabledInNativeImage`
  * Look at framework modules, such as Micronaut Test Resources or Quarkus Dev Services
* Deployment 📦
  * 👩‍💻 Packaging and linking options
    * JDK 
    * JDK | Distroless
    * JDK | JLink
    * Native
      * Dynamic linking: smallest binaries, fastest builds, no special toolchain. Needs shared libs provided by the excution platform.
      * Mostly-static: statically links everything except the C standard library (libc). Fewer runtime dependencies, smaller than fully static, but still requires libc and not as portable as fully static.
      * Fully static: fully self-contained, runs on scratch, highly portable. Larger binaries, requires recompiling for both code & patch updates.
      *  Demo: [native-spring-boot](https://github.com/alina-yur/native-spring-boot) `docker images | grep "native-spring"`
    * Recipe for the smallest container images: Native | Static | From scratch | Optimize for size
  * Scan the images for vulnerabilities & packages 🛡️
  * Buildpacks
  * GitHub actions
* Security 🛡️
  *  Security by design and by default
  * 👩‍💻 Vulnerability Scanning
    * `grype`: `native-image-inspect --sbom ./target/demo-sbom | grype -v`
    * `native-image-inspect --sbom ./target/demo-sbom > output.json`
  * 👩‍💻 SBOM support: exposing in Spring Actuator
  * 👩‍💻 SBOM support in Micronaut
  * Demo: [native-spring-boot-sbom](https://github.com/alina-yur/native-spring-boot-sbom)
  * Adavanced obfuscation: `-H:AdvancedObfuscation=`

* Monitoring 📈
  * 👩‍💻 Micrometer
    * (Tempo -> service graph)
  * 👩‍💻 `jvmstat` <!-- visualvm --jdkhome /Users/ayurenko/.sdkman/candidates/java/23-graal --> 
  * JFR, JMX, `jcmd`
  * `perf stat <./target/demo>`
  * LCOV?
* Tooling 🔮
  * 👩‍💻 Build reports
* GraalVM 25 🐰
  * [`-H:Preserve=all`](https://github.com/oracle/graal/pull/10180)
  * Refined ML profile inference for even higher performanvce out of the box, on by default in `-03`
   * Enable in `-O2`: `-H:+MLProfileInferenceUseGNNModel`
  * Use ML for smaller executables: `-H:+MLCallCountProfileInference`
  * Advanced obfuscation and security by default
  * Fast PIE with `-H:+RelativeCodePointers`
  * FFM and Vector API support updates
  * DX updates 🛠️
  * Embedding Python (Pygal charts) (or airline demo with qr code)
* Future work 👩‍🔬
 * [Project Crema](https://github.com/orgs/oracle/projects/6?pane=issue&itemId=113766307&issue=oracle%7Cgraal%7C11327)
 * [Native Image Layers](https://github.com/oracle/graal/issues/7626)
 * Web Image (javac)
 


To do 

[] rebuild container images
[] rebuild petclnici with various linking options
[] rebuild petclinic
[] petclinic LCOV
[] embedding demo
[] obfuscation demo - SecretClass
