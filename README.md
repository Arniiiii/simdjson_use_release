# simdjson_use_release

It's a project that ensures that you use release version of simdjson.h and simdjson.cpp .

# How to use

1. Add the project via CPM or add_subdirectory or whatever and set in options `simdjson_VERSION` to something like `3.10.1`
2. Link the `simdjson` to your project.
3. Include `simdjson.h` in your header files.

# License

If it's code related only to this repo: Unlicense

If it's simdjson's code: [license that simdjson uses](https://github.com/simdjson/simdjson/blob/master/LICENSE)
