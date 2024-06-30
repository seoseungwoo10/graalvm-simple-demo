## 1. GraalVM 샘플 관련

- [GraalVM](https://www.graalvm.org/)은 Oracle에서 개발한 JVM 기반의 다양한 언어를 지원하는 통합 개발 환경이다.
- GraalVM은 Java, Scala, Kotlin, Groovy, Clojure, JavaScript, Ruby, R, Python, WebAssembly 등 다양한 언어를 지원한다.
- GraalVM은 JIT 컴파일러, AOT 컴파일러, 원격 디버깅, 프로파일링, 통합 테스트, 메모리 프로파일링, 힙 덤프 분석, 다양한 언어 지원, 다양한 플랫폼 지원 등의 기능을 제공한다.
- GraalVM은 Java 17, Java 19, Java 21, Java 22을 지원한다.
- GraalVM은 Community Edition과 Enterprise Edition으로 나뉘며, Enterprise Edition은 상용 라이선스로 제공된다.
- GraalVM은 다양한 언어를 지원하며, 다양한 플랫폼에서 실행할 수 있다.

### `org.apache.maven.archetypes:maven-archetype-quickstart`으로 소스코드 생성

```bash
mvn archetype:generate -DgroupId=com.example -DartifactId=graalvm-sample -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

### 컴파일러 변경

컴파일러 변경을 위해 환경변수 추가.

```shell
set Path=C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Auxiliary\Build;%Path%
```

### graalvm 환경변수 설정

```shell
C:\Users\kangchun>C:\Java\jdk_bat\graalvm21.bat
Java 21 activated.
java version "21.0.3" 2024-04-16 LTS
Java(TM) SE Runtime Environment Oracle GraalVM 21.0.3+7.1 (build 21.0.3+7-LTS-jvmci-23.1-b37)
Java HotSpot(TM) 64-Bit Server VM Oracle GraalVM 21.0.3+7.1 (build 21.0.3+7-LTS-jvmci-23.1-b37, mixed mode, sharing)


```

### maven 컴파일

```bash
mvn clean package
```

### GraalVM Native Image 생성 (윈도우)

```bash
# graalvm 21.0.0 버전 이후로는 --no-server 옵션을 사용하지 않아도 된다.
native-image -H:Class=org.example.App -cp graalvm-simple-demo-1.0.jar
native-image -jar graalvm-simple-demo-1.0.jar

```

### 오류 (24/06/29)

**오류 발생**  

```log
Error: Failed to find 'vcvarsall.bat' in a Visual Studio installation.
Please make sure that Visual Studio 2022 version 17.1.0 or later is installed on your system. 
You can download it at https://visualstudio.microsoft.com/downloads/. If this error persists, 
please try and run GraalVM Native Image in an x64 Native Tools Command Prompt or file a ticket.
```

"Failed to find 'vcvarsall.bat' in a Visual Studio installation" 이라는 오류는 GraalVM이 네이티브 이미지를 생성하는데 필요한 Microsoft Visual C++ 컴파일러를 찾지 못했을 때 발생합니다.

**해결 방법:**

GraalVM에서 네이티브 이미지를 빌드하려면 다음 두 가지 구성 요소를 모두 설치해야 합니다.

1. **Visual Studio 2022 (버전 17.1.0 이상):**

* Visual Studio 2022를 설치할 때, "C++를 사용한 데스크톱 개발" 워크로드를 선택해야 합니다. 이 워크로드에는 vcvarsall.bat 파일과 필요한 C++ 빌드 도구가 포함되어 있습니다.

2. **Microsoft Visual C++ Build Tools:**

* Visual Studio 2022를 설치하지 않고 빌드 도구만 설치하려면 Microsoft Visual C++ Build Tools를 다운로드하여 설치할 수 있습니다.
* 설치 중 "C++ 빌드 도구" 워크로드를 선택해야 합니다.

**설치 후 확인:**

* 설치가 완료된 후, 새로운 명령 프롬프트 창을 열고 `native-image` 명령을 다시 실행해 봅니다.
* `vcvarsall.bat` 파일이 Visual Studio 설치 디렉토리 (예: `C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build`) 에 있는지 확인합니다.
* `x64 Native Tools Command Prompt for VS 2022`를 사용하여 명령을 실행하는 것이 좋습니다. 이는 Visual C++ 빌드 환경을 자동으로 설정해줍니다.

**추가 정보:**

* 만약 Visual Studio 2022를 설치했지만 오류가 지속된다면, `vcvarsall.bat` 파일의 경로가 GraalVM에서 제대로 인식되는지 확인해야 합니다.
* GraalVM 문서에서 Windows 환경의 Native Image 생성에 대한 자세한 정보를 확인할 수 있습니다.

### 경고

**경고 발생**

```log
Warning: Ignoring server-mode native-image argument --no-server.
```

**해결 방법:**

"Warning: Ignoring server-mode native-image argument --no-server." 경고는 GraalVM 22.1 버전부터 native-image 명령어의 기본 동작 방식이 변경되었기 때문에 발생합니다. 이전 버전에서는 native-image 명령을 실행하면 자동으로 GraalVM의 서버 모드가 활성화되었지만, GraalVM 22.1부터는 기본적으로 서버 모드가 비활성화됩니다.


## 2. native-image 주요 옵션 설명

`native-image` 명령어는 다양한 옵션을 제공하여 네이티브 이미지 생성 과정을 세밀하게 제어할 수 있도록 합니다. 주요 옵션과 자세한 설명은 다음과 같습니다.

**1. 기본 옵션:**

* `-H:Class=<클래스 이름>`: 메인 클래스를 명시적으로 지정합니다.
* `-cp <경로>` 또는 `--class-path <경로>`: 컴파일할 클래스 파일 또는 JAR 파일의 경로를 지정합니다.
* `-jar <JAR 파일>`: JAR 파일을 직접 지정하여 네이티브 이미지를 생성합니다.
* `-H:Name=<실행 파일 이름>`: 생성될 네이티브 실행 파일의 이름을 지정합니다.
* `-H:+ReportUnsupportedElementsAtRuntime`: 지원되지 않는 기능 사용 시 런타임에 오류 메시지를 출력합니다.
* `--no-fallback`: 폴백 이미지 생성을 비활성화합니다. (성능 향상)
* `-H:+StaticExecutableWithMusl` (Linux): musl libc를 사용하여 정적으로 링크된 실행 파일을 생성합니다.

**2. 고급 옵션:**

* `-H:ReflectionConfigurationFiles=<JSON 파일 경로>`: 리플렉션 설정 파일을 지정합니다.
* `-H:JNIConfigurationFiles=<JSON 파일 경로>`: JNI 설정 파일을 지정합니다.
* `-H:ResourceConfigurationFiles=<JSON 파일 경로>`: 리소스 설정 파일을 지정합니다.
* `-H:+TraceClassInitialization`: 클래스 초기화 과정을 추적합니다. (디버깅)
* `-H:+PrintAnalysisCallTree`: 분석 호출 트리를 출력합니다. (디버깅)

**3. 서버 모드 옵션:**

* `-H:+SpawnIsolates`: GraalVM 서버 모드를 활성화합니다. (빌드 속도 향상)

**4. 기타 옵션:**

* `--help`: 도움말 메시지를 출력합니다.
* `--help-extra`: 추가 옵션에 대한 도움말 메시지를 출력합니다.
* `--version`: GraalVM 버전 정보를 출력합니다.

**주요 옵션 상세 설명:**

* **`-H:Class=<클래스 이름>`:** JAR 파일의 MANIFEST.MF에 `Main-Class` 속성이 없거나, 여러 개의 메인 클래스가 있는 경우 명시적으로 지정해야 합니다.
* **`-H:+ReportUnsupportedElementsAtRuntime`:** 네이티브 이미지 생성 시 지원되지 않는 기능(리플렉션, JNI 등)을 사용하는 경우 런타임에 오류 메시지를 출력하여 문제 해결에 도움을 줍니다.
* **`--no-fallback`:** 폴백 이미지 생성을 비활성화하여 네이티브 이미지의 크기를 줄이고 시작 시간을 단축할 수 있습니다. 단, 일부 기능 제한이 있을 수 있습니다.
* **`-H:+StaticExecutableWithMusl` (Linux):** musl libc를 정적으로 링크하여 완전히 독립적인 실행 파일을 생성합니다. 다른 시스템에 glibc가 설치되어 있지 않아도 실행 가능합니다.

**주의 사항:**

* 옵션은 GraalVM 버전에 따라 변경될 수 있습니다. 최신 정보는 GraalVM 공식 문서를 참조하십시오.
* 일부 옵션은 특정 플랫폼에서만 사용 가능합니다.

**참고 자료:**

* **GraalVM Native Image 옵션:** [https://www.graalvm.org/22.0/reference-manual/native-image/Options/](https://www.graalvm.org/22.0/reference-manual/native-image/Options/)

## 3. Ubuntu 환경에서 native-image로 실행  

```shell
export PATH=<GRAALVM_HOME>/bin:$PATH
```  

```shell
# glibc-devel 및 zlib-devel 설치 (필수):
sudo apt-get install build-essential libz-dev
# (선택 사항) musl-tools 설치:
sudo apt-get install musl-tools
```

```shell
# graalvm 21.0.0 버전 이후로는 --no-server 옵션을 사용하지 않아도 된다.
native-image -H:Class=org.example.App -cp graalvm-simple-demo-1.0.jar
native-image -jar graalvm-simple-demo-1.0.jar
```


