# emscripten-tools

Collection of tools for emscripten.

## empack

Package an emscripten-built project into a nice shell, suitable for embedding in a website or itch.io.

Usage: `empack <input.{wasm,html,js}> <output.zip>`.

A loading screen will be shown for both the main wasm file and [preloaded data](https://emscripten.org/docs/porting/files/packaging_files.html#preloading-files).

The main `.wasm` file will be gzipped and archived as `.wasmz`.
This is to ensure that the browser will always report the download progress instead of buffering the entire file until completion.
When uploaded to itch.io, certain [files types](https://itch.io/docs/creators/html5#compression) will have the `Content-Encoding` header set to `gzip`.
The loader is aware of that and will always report the correct progress against the unpacked size.
`WebAssembly.instantiateStreaming` is always used regardless of `Content-Encoding` to ensure fast startup time.

The preloaded `.data` file will be packed as `.data.pck`.
This hints itch.io to apply gzip compression.
As emscripten internal loader is aware of the unpacked size, the reported progress will be accurate.

### Customization

Just copy `empack` and the files in `shell` into your project and modify them.

## emserve

Serve a zip file packaged by empack.
This is only suitable for development and not production.

[Cross-origin headers](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now#security_requirements) are automatically added.

The zip file is also checked for modification and reloaded if needed.
