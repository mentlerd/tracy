cmake_minimum_required(VERSION 3.23)

# set the project name
project(Tracy)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# External dependencies
find_package(glfw3 REQUIRED)
find_package(OpenGL REQUIRED)

find_package(PkgConfig REQUIRED)
pkg_search_module(capstone REQUIRED IMPORTED_TARGET capstone)

# Packaged in dependencies
add_library(ImGui STATIC
	../imgui/imgui.cpp
	../imgui/imgui_demo.cpp
	../imgui/imgui_draw.cpp
	../imgui/imgui_tables.cpp
	../imgui/imgui_widgets.cpp
)
target_include_directories(ImGui PUBLIC
	../imgui
)

add_library(nfd STATIC
	../nfd/nfd_common.c
	../nfd/nfd_cocoa.m
)
target_link_libraries(nfd PUBLIC
	"-framework AppKit"
	"-framework CoreFoundation"
)

add_library(zstd STATIC
	../zstd/common/debug.c
	../zstd/common/entropy_common.c
	../zstd/common/error_private.c
	../zstd/common/fse_decompress.c
	../zstd/common/pool.c
	../zstd/common/threading.c
	../zstd/common/xxhash.c
	../zstd/common/zstd_common.c
	../zstd/compress/fse_compress.c
	../zstd/compress/hist.c
	../zstd/compress/huf_compress.c
	../zstd/compress/zstdmt_compress.c
	../zstd/compress/zstd_compress.c
	../zstd/compress/zstd_compress_literals.c
	../zstd/compress/zstd_compress_sequences.c
	../zstd/compress/zstd_compress_superblock.c
	../zstd/compress/zstd_double_fast.c
	../zstd/compress/zstd_fast.c
	../zstd/compress/zstd_lazy.c
	../zstd/compress/zstd_ldm.c
	../zstd/compress/zstd_opt.c
	../zstd/decompress/huf_decompress.c
	../zstd/decompress/zstd_ddict.c
	../zstd/decompress/zstd_decompress.c
	../zstd/decompress/zstd_decompress_block.c
	../zstd/dictBuilder/cover.c
	../zstd/dictBuilder/divsufsort.c
	../zstd/dictBuilder/fastcover.c
	../zstd/dictBuilder/zdict.c
)

# Internal components
add_library(tracy-common STATIC
	../common/tracy_lz4.cpp
	../common/tracy_lz4.hpp
	../common/tracy_lz4hc.cpp
	../common/tracy_lz4hc.hpp

	../common/TracyAlign.hpp
	../common/TracyAlloc.hpp
	../common/TracyApi.h
	../common/TracyColor.hpp
	../common/TracyForceInline.hpp
	../common/TracyMutex.hpp
	../common/TracyProtocol.hpp
	../common/TracyQueue.hpp
	../common/TracySocket.cpp
	../common/TracySocket.hpp
	../common/TracyStackFrames.cpp
	../common/TracyStackFrames.hpp
	../common/TracySystem.cpp
	../common/TracySystem.hpp
	../common/TracyUwp.hpp
	../common/TracyYield.hpp
)

add_library(tracy-server STATIC
	../server/IconsFontAwesome5.h
	../server/tracy_pdqsort.h
	../server/tracy_robin_hood.h
	../server/tracy_xxhash.h
	
	../server/TracyBadVersion.cpp
	../server/TracyBadVersion.hpp	
	../server/TracyBuzzAnim.hpp	
	../server/TracyCharUtil.hpp	
	../server/TracyColor.cpp
	../server/TracyColor.hpp
	../server/TracyDecayValue.hpp	
	../server/TracyEvent.hpp	
	../server/TracyEventDebug.cpp
	../server/TracyEventDebug.hpp
	../server/TracyFileHeader.hpp	
	../server/TracyFileRead.hpp	
	../server/TracyFilesystem.cpp
	../server/TracyFilesystem.hpp
	../server/TracyFileWrite.hpp
	../server/TracyImGui.hpp
	../server/TracyMemory.cpp
	../server/TracyMemory.hpp
	../server/TracyMicroArchitecture.cpp
	../server/TracyMicroArchitecture.hpp
	../server/TracyMmap.cpp
	../server/TracyMmap.hpp
	../server/TracyMouse.cpp
	../server/TracyMouse.hpp
	../server/TracyPopcnt.hpp
	../server/TracyPrint.cpp
	../server/TracyPrint.hpp
	../server/TracyShortPtr.hpp
	../server/TracySlab.hpp
	../server/TracySort.hpp
	../server/TracySortedVector.hpp
	../server/TracySourceContents.cpp
	../server/TracySourceContents.hpp
	../server/TracySourceTokenizer.cpp
	../server/TracySourceTokenizer.hpp
	../server/TracySourceView.cpp
	../server/TracySourceView.hpp
	../server/TracyStorage.cpp
	../server/TracyStorage.hpp
	../server/TracyStringDiscovery.hpp
	../server/TracyTaskDispatch.cpp
	../server/TracyTaskDispatch.hpp
	../server/TracyTexture.cpp
	../server/TracyTexture.hpp
	../server/TracyTextureCompression.cpp
	../server/TracyTextureCompression.hpp
	../server/TracyThreadCompress.cpp
	../server/TracyThreadCompress.hpp
	../server/TracyUserData.cpp
	../server/TracyUserData.hpp
	../server/TracyVarArray.hpp
	../server/TracyVector.hpp
	../server/TracyVersion.hpp
	../server/TracyView.cpp
	../server/TracyView.hpp
	../server/TracyViewData.hpp
	../server/TracyWeb.cpp
	../server/TracyWeb.hpp
	../server/TracyWorker.cpp
	../server/TracyWorker.hpp
)
target_link_libraries(tracy-server PUBLIC 
	PkgConfig::capstone ImGui
)

# Profiler
add_executable(profiler
	src/DroidSans.hpp
	src/FiraCodeRetina.hpp
	src/FontAwesomeSolid.hpp
	src/HttpRequest.cpp
	src/HttpRequest.hpp
	src/icon.hpp
	src/imgui_impl_glfw.cpp
	src/imgui_impl_glfw.h
	src/imgui_impl_opengl3.cpp
	src/imgui_impl_opengl3.h
	src/imgui_impl_opengl3_loader.h
	src/main.cpp
	src/NativeWindow.cpp
	src/NativeWindow.hpp
	src/ResolvService.cpp
	src/ResolvService.hpp
	src/stb_image.h
)

target_link_libraries(profiler PRIVATE
	glfw
	OpenGL::GL
	ImGui
	nfd
	zstd
	tracy-common
	tracy-server
)
