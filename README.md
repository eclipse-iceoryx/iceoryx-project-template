# Project template to easily get started with iceoryx

This is a small template to easily get started with iceoryx. Just fork the repo
and start to develop your iceoryx based applications.

Just run `cmake -B build && cmake --build build`. If iceoryx is not found on the system,
it is automatically fetched from github and build.

In case you want to use an already pre-build iceoryx, just specify the `CMAKE_PREFIX_PATH`
like `cmake -DCMAKE_PREFIX_PATH=/path/to/installed/iceoryx -B build && cmake --build build`.

The `src` folder contains two simple applications, one with a subscriber and one with a
publisher.

Happy Hacking
