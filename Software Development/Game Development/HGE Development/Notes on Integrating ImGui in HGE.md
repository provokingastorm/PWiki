These are the step I took to integrate the ImGui framework in HGE. ImGui is a framework used for implementing visualizers within your game. Typically, this means debugging tools, but it doesn't have to be. The framework emphasizes easy integration and low-impact on your codebase. That said, there is a performance hit from running this framework. It's not surprising given the benefits of having a powerful UI toolkit that doesn't require a messy integration. It's recommended to use for development purposes only. Compile it out for release builds. 

**Last Edited**: 2026/07/23

## Steps
****
1\. Read the excellent [documentation](https://github.com/ocornut/imgui/tree/master/docs) on GitHub. 
2\. Clone the [ImGui GitHub](https://github.com/ocornut/imgui/tree/master) repository. 
3\. Copy all the source code from the root directory of the ImGui repository. 
4\. Paste all those files to a new folder located here: `%HGE_ROOT%/src/imgui`.
5\. Copy the `imgui_impl_win32.h` and `imgui_impl_dx9.h` files from ImGui's `backends` sub-folder in the repository.
6\. Add the newly-created `imgui` source folder as a subdirectory in the root `CMakeLists.txt` file in the HGE root folder. 
   
	`add_subdirectory(src/imgui)`
   
7\. Add the `imgui` source folder as an `include_directory` in the `CMakeLists.txt` in the `%HGE_ROOT%/src/core` folder, as well as any other library in your codebase using ImGui.
   
   `include_directories(${HGE_SOURCE_DIR}/src/imgui)`
   
8\. Rebuild the project using cmake.
9\. In `src/core/graphics.cpp`, add the following header files at the top of the file.

`#include "imgui_impl_win32.h"`
`#include "imgui_impl_dx9.h"`

10\. In `src/core/graphics.cpp` add the following code to `HGE_Impl::gfx_init()` before `Gfx_Clear()` is called:

`ImGui_ImplDX9_Init(d3d_device_);`

11\. In `src/core/graphics.cpp`, add the following code to `HGE_Impl::Gfx_BeginScene(...)` before `d3d_device_->BeginScene()` gets called:

`ImGui_ImplDX9_NewFrame();`
`ImGui_ImplWin32_NewFrame();`
`ImGui::NewFrame();`

11\. In `src/core/graphics.cpp`, add the following code to `HGE_Impl::Gfx_EndScene()` before `d3d_device_->EndScene()` gets called:

`ImGui::EndFrame();`
`ImGui::Render();`
`ImGui_ImplDX9_RenderDrawData(ImGui::GetDrawData());`

12\. In `src/core/graphics.cpp`, add the following code to `HGE_Impl::gfx_done()` before `d3d_device_->Release(...)` gets called:

`ImGui_ImplDX9_Shutdown();`

13\. In `src/core/graphics.cpp`, add the following code to `HGE_Impl::gfx_restore()` before `d3d_device_->Reset(...)` gets called:

`ImGui_ImplDX9_InvalidateDeviceObjects();`

14\. In `src/core/graphics.cpp`, add the following code to `HGE_Impl::gfx_restore()` *after* `d3d_device_->Reset(...)` gets called:

`ImGui_ImplDX9_CreateDeviceObjects();`

15\. In `src/core/system.cpp`, add the following header files at the top of the file.

`#include "imgui_impl_win32.h"`
`#include "imgui_impl_dx9.h"`

16\. In `src/core/system.cpp`, add the following code to `HGE_Impl::System_Initiate()` *after* `CreateWindowEx(...)` gets called:

`IMGUI_CHECKVERSION();`
`ImGui::CreateContext();`
`ImGuiIO& io = ImGui::GetIO(); (void)io;`
`io.ConfigFlags |= ImGuiConfigFlags_NavEnableKeyboard;`
`io.ConfigFlags |= ImGuiConfigFlags_NavEnableGamepad;`

`ImGui_ImplWin32_Init(hwnd_);`

17\. In `src/core/system.cpp`, add the following code to `HGE_Impl::System_Shutdown()` before `DestroyWindow(...)` gets called:

`ImGui_ImplWin32_Shutdown();`
`ImGui::DestroyContext();`


17\. In `src/core/system.cpp`, add the following code to `HGE_Impl::window_proc(...)` at the beginning of the function before the message is processed:

`extern IMGUI_IMPL_API LRESULT ImGui_ImplWin32_WndProcHandler(HWND hWnd, UINT msg, WPARAM wParam, LPARAM lParam);`
`if (ImGui_ImplWin32_WndProcHandler(hwnd, msg, wparam, lparam))`
`  return true;`

## Summary
****
That's it! Considering how a DirectX application is structured, that a very reasonable integration. 
