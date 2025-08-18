Great choice 👍 — rendering a **triangle** is the classic first step in graphics programming!

Here’s a **DirectX 9 example** that sets up a vertex buffer and draws a colored triangle inside the window:

---

## 📝 DirectX9 Example — Render a Triangle

```cpp
#include <windows.h>
#include <d3d9.h>

// Global variables
LPDIRECT3D9 d3d = NULL;
LPDIRECT3DDEVICE9 d3dDevice = NULL;
LPDIRECT3DVERTEXBUFFER9 v_buffer = NULL;

// Custom vertex format
struct CUSTOMVERTEX
{
    FLOAT x, y, z, rhw;  // Position
    DWORD color;         // Vertex color
};
#define CUSTOMFVF (D3DFVF_XYZRHW | D3DFVF_DIFFUSE)

// Forward declarations
LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);
HRESULT InitD3D(HWND hWnd);
VOID InitGraphics();
VOID RenderFrame();
VOID CleanD3D();

// Entry point
int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance,
                   LPSTR lpCmdLine, int nCmdShow)
{
    // Register window class
    WNDCLASSEX wc = { sizeof(WNDCLASSEX), CS_CLASSDC, WndProc,
                      0L, 0L, GetModuleHandle(NULL), NULL, NULL, NULL, NULL,
                      "DX9TriangleClass", NULL };
    RegisterClassEx(&wc);

    // Create window
    HWND hWnd = CreateWindow("DX9TriangleClass", "DirectX9 - Triangle",
                             WS_OVERLAPPEDWINDOW, 100, 100, 800, 600,
                             NULL, NULL, wc.hInstance, NULL);

    // Initialize Direct3D
    if (SUCCEEDED(InitD3D(hWnd)))
    {
        InitGraphics();
        ShowWindow(hWnd, nCmdShow);
        UpdateWindow(hWnd);

        // Main message loop
        MSG msg;
        ZeroMemory(&msg, sizeof(msg));
        while (msg.message != WM_QUIT)
        {
            if (PeekMessage(&msg, NULL, 0U, 0U, PM_REMOVE))
            {
                TranslateMessage(&msg);
                DispatchMessage(&msg);
            }
            else
            {
                RenderFrame(); // Draw each frame
            }
        }
    }

    UnregisterClass("DX9TriangleClass", wc.hInstance);
    return 0;
}

// Initialize Direct3D
HRESULT InitD3D(HWND hWnd)
{
    if ((d3d = Direct3DCreate9(D3D_SDK_VERSION)) == NULL)
        return E_FAIL;

    D3DPRESENT_PARAMETERS d3dpp;
    ZeroMemory(&d3dpp, sizeof(d3dpp));
    d3dpp.Windowed = TRUE;
    d3dpp.SwapEffect = D3DSWAPEFFECT_DISCARD;
    d3dpp.BackBufferFormat = D3DFMT_UNKNOWN;

    if (FAILED(d3d->CreateDevice(D3DADAPTER_DEFAULT, D3DDEVTYPE_HAL, hWnd,
                                 D3DCREATE_SOFTWARE_VERTEXPROCESSING,
                                 &d3dpp, &d3dDevice)))
    {
        return E_FAIL;
    }

    return S_OK;
}

// Create a triangle in a vertex buffer
VOID InitGraphics()
{
    // Create vertex buffer (3 vertices, each is CUSTOMVERTEX)
    d3dDevice->CreateVertexBuffer(3 * sizeof(CUSTOMVERTEX),
                                  0, CUSTOMFVF,
                                  D3DPOOL_MANAGED,
                                  &v_buffer, NULL);

    // Fill vertex buffer
    CUSTOMVERTEX vertices[] =
    {
        { 320.0f,  100.0f, 0.5f, 1.0f, D3DCOLOR_XRGB(255,0,0), },  // Red
        { 520.0f,  400.0f, 0.5f, 1.0f, D3DCOLOR_XRGB(0,255,0), },  // Green
        { 120.0f,  400.0f, 0.5f, 1.0f, D3DCOLOR_XRGB(0,0,255), },  // Blue
    };

    VOID* pVoid;    // pointer to memory
    v_buffer->Lock(0, 0, (void**)&pVoid, 0);
    memcpy(pVoid, vertices, sizeof(vertices));
    v_buffer->Unlock();
}

// Render a single frame
VOID RenderFrame()
{
    if (!d3dDevice) return;

    // Clear the backbuffer to dark blue
    d3dDevice->Clear(0, NULL, D3DCLEAR_TARGET,
                     D3DCOLOR_XRGB(0, 40, 100), 1.0f, 0);

    d3dDevice->BeginScene();

    // Set the vertex stream
    d3dDevice->SetStreamSource(0, v_buffer, 0, sizeof(CUSTOMVERTEX));
    d3dDevice->SetFVF(CUSTOMFVF);

    // Draw triangle (3 vertices = 1 primitive)
    d3dDevice->DrawPrimitive(D3DPT_TRIANGLELIST, 0, 1);

    d3dDevice->EndScene();
    d3dDevice->Present(NULL, NULL, NULL, NULL);
}

// Cleanup
VOID CleanD3D()
{
    if (v_buffer) v_buffer->Release();
    if (d3dDevice) d3dDevice->Release();
    if (d3d) d3d->Release();
}

// Windows procedure
LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wParam, LPARAM lParam)
{
    switch (msg)
    {
    case WM_DESTROY:
        CleanD3D();
        PostQuitMessage(0);
        return 0;
    }
    return DefWindowProc(hWnd, msg, wParam, lParam);
}
```

---

## 🔨 Build Instructions

1. Make a new **Win32 Project** in Visual Studio.
    
2. Replace the code with the above.
    
3. Add these to **Linker → Input → Additional Dependencies**:
    
    ```
    d3d9.lib
    d3dx9.lib
    ```
    
4. Build & run → you should see a **red, green, and blue triangle** rendered on a dark blue background 🎉
    

---

👉 Would you like me to take this further and show you how to **rotate the triangle in 3D space** with a camera + transformation matrices, or keep it in 2D for now?