Build OpenCV.js {#tutorial_js_setup}
===============================


Installing Emscripten
-----------------------------

[Emscripten](https://github.com/kripken/emscripten) is an LLVM-to-JavaScript compiler. We will use Emscripten to build OpenCV.js.

To Install Emscripten, follow instructions of [Emscripten SDK](https://kripken.github.io/emscripten-site/docs/getting_started/downloads.html).

For example:
@code{.bash}
./emsdk update
./emsdk install latest
./emsdk activate latest
@endcode

@note
To compile to [WebAssembly](http://webassembly.org), you need to install and activate [Binaryen](https://github.com/WebAssembly/binaryen) with the `emsdk` command. Please refer to [Developer's Guide](http://webassembly.org/getting-started/developers-guide/) for more details.

After install, ensure the `EMSCRIPTEN` environment is setup correctly.

For example:
@code{.bash}
source ./emsdk_env.sh
echo ${EMSCRIPTEN}
@endcode

Obtaining OpenCV Source Code
--------------------------

You can use the latest stable OpenCV version or you can grab the latest snapshot from our [Git
repository](https://github.com/opencv/opencv.git).

### Obtaining the Latest Stable OpenCV Version

-   Go to our [releases page](http://opencv.org/releases.html).
-   Download the source archive and unpack it.

### Obtaining the Cutting-edge OpenCV from the Git Repository

Launch Git client and clone [OpenCV repository](http://github.com/opencv/opencv).

For example:
@code{.bash}
git clone https://github.com/opencv/opencv.git
@endcode

@note
It requires `git` installed in your development environment.

Building OpenCV.js from Source
---------------------------------------

-#  To build `opencv.js`, execute python script `<opencv_src_dir>/platforms/js/build_js.py <build_dir>`.

    For example, to build in `build_js` directory:
    @code{.bash}
    cd opencv
    python ./platforms/js/build_js.py build_js
    @endcode

    @note
    It requires `python` and `cmake` installed in your development environment.

-#  The build script builds asm.js version by default. To build WebAssembly version, append `--build_wasm` switch.

    For example, to build wasm version in `build_wasm` directory:
    @code{.bash}
    python ./platforms/js/build_js.py build_wasm --build_wasm
    @endcode

-#  [optional] To build documents, append `--build_doc` option.

    For example:
    @code{.bash}
    python ./platforms/js/build_js.py build_js --build_doc
    @endcode

    @note
    It requires `doxygen` installed in your development environment.

-#  [optional] To build tests, append `--build_test` option.

    For example:
    @code{.bash}
    python ./platforms/js/build_js.py build_js --build_test
    @endcode

    To run tests, launch a local web server in \<build_dir\>/bin folder. For example, node http-server which serves on `localhost:8080`.

    Navigate the web browser to `http://localhost:8080/tests.html`, which runs the unit tests automatically.

    You can also run tests using Node.js.

    For example:
    @code{.sh}
    cd bin
    npm install
    node tests.js
    @endcode

    @note
    It requires `node` installed in your development environment.

-#  [optional] To build `opencv.js` with threads optimization, append `--threads` option.

    For example:
    @code{.bash}
    python ./platforms/js/build_js.py build_js --build_wasm --threads
    @endcode

    The default threads number is the logic core number of your device. You can use `cv.parallel_pthreads_set_threads_num(number)` to set threads number by yourself and use `cv.parallel_pthreads_get_threads_num()` to get the current threads number.

    @note
    You should build wasm version of `opencv.js` if you want to enable this optimization. And the threads optimization only works in browser, not in node.js. You need to enable the `WebAssembly threads support` feature first with your browser. For example, if you use chrome, please enable this flag in chrome://flags.

-#  [optional] To build `opencv.js` with wasm simd optimization, append `--simd` option.

    For example:
    @code{.bash}
    python ./platforms/js/build_js.py build_js --build_wasm --simd
    @endcode

    The simd optimization is experimental as wasm simd is still in development.

    @note
    Now only emscripten LLVM upstream backend supports wasm simd, refering to https://emscripten.org/docs/porting/simd.html. So you need to setup upstream backend environment with the following command first: 
    @code{.bash}
    ./emsdk update
    ./emsdk install latest-upstream
    ./emsdk activate latest-upstream
    source ./emsdk_env.sh
    @endcode

    @note
    You should build wasm version of `opencv.js` if you want to enable this optimization. For browser, you need to enable the `WebAssembly SIMD support` feature first. For example, if you use chrome, please enable this flag in chrome://flags. For Node.js, you need to run script with flag `--experimental-wasm-simd`.

-#  [optional] To build wasm intrinsics tests, append `--build_wasm_intrin_test` option.

    For example:
    @code{.bash}
    python ./platforms/js/build_js.py build_js --build_wasm --simd --build_wasm_intrin_test
    @endcode

    For wasm intrinsics tests, you can use the following function to test all the cases:
    @code{.js}
    cv.test_hal_intrin_all()
    @endcode

    And the failed cases will be logged in the JavaScript debug console.
    
    If you only want to test single data type of wasm intrinsics, you can use the following functions: 
    @code{.js}
    cv.test_hal_intrin_uint8()
    cv.test_hal_intrin_int8()
    cv.test_hal_intrin_uint16()
    cv.test_hal_intrin_int16()
    cv.test_hal_intrin_uint32()
    cv.test_hal_intrin_int32()
    cv.test_hal_intrin_uint64()
    cv.test_hal_intrin_int64()
    cv.test_hal_intrin_float32()
    cv.test_hal_intrin_float64()
    @endcode

-#  [optional] To build performance tests, append `--build_perf` option.

    For example:
    @code{.bash}
    python ./platforms/js/build_js.py build_js --build_perf
    @endcode

    To run performance tests, launch a local web server in \<build_dir\>/bin folder. For example, node http-server which serves on `localhost:8080`.

    There are some kernels now in the performance test like `cvtColor`, `resize` and `threshold`. For example, if you want to test `threshold`, please navigate the web browser to `http://localhost:8080/perf/perf_imgproc/perf_threshold.html`. You need to input the test parameter like `(1920x1080, CV_8UC1, THRESH_BINARY)`, and then click the `Run` button to run the case. And if you don't input the parameter, it will run all the cases of this kernel.

    You can also run tests using Node.js.

    For example, run `threshold` with parameter `(1920x1080, CV_8UC1, THRESH_BINARY)`:
    @code{.sh}
    cd bin/perf
    npm install
    node perf_threshold.js --test_param_filter="(1920x1080, CV_8UC1, THRESH_BINARY)"
    @endcode
