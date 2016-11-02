Here's the list of files that must be merged

```
atom/app/atom_main_delegate.cc
atom/browser/api/atom_api_web_contents.cc
atom/browser/api/atom_api_web_contents.h
atom/browser/api/atom_api_window.cc
atom/browser/api/atom_api_window.h
atom/browser/atom_browser_client.cc
atom/browser/atom_browser_client.h
atom/browser/native_window.cc
atom/browser/web_contents_preferences.cc
atom/browser/web_contents_preferences.h
atom/common/api/atom_api_v8_util.cc
atom/common/options_switches.cc
atom/common/options_switches.h
atom/renderer/api/atom_api_renderer_ipc.cc
atom/renderer/api/atom_api_renderer_ipc.h
atom/renderer/atom_render_view_observer.cc
atom/renderer/atom_render_view_observer.h
atom/renderer/atom_sandboxed_renderer_client.cc
atom/renderer/atom_sandboxed_renderer_client.h
electron.gyp
filenames.gypi
lib/browser/api/browser-window.js
lib/browser/chrome-extension.js
lib/browser/guest-window-manager.js
lib/browser/rpc-server.js
lib/renderer/api/ipc-renderer.js
lib/renderer/api/ipc-renderer-setup.js
lib/sandboxed_renderer/init.js
package.json
script/bootstrap.py
script/cibuild
spec/api-browser-window-spec.js
spec/fixtures/api/sandbox.html
spec/fixtures/module/preload-sandbox.js
tools/js2c.py
```

Here's a list of commits
```
commit 47fd41715f2e8ac60429f798092348178418fd3a
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Mon Sep 26 17:40:45 2016 -0300

    Add some tests for "sandbox" option
    
commit a64978b812848e037a9ff81b1b4e2555aff8ad87
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Mon Sep 5 22:28:40 2016 -0300

    Use the routing id on api::WebContents::GetID
    
    The sandbox option allows multiple webContents in one renderer process, so using
    the only the renderer id to identify WebContents instances is no longer an
    option.
    
    WebContents::GetID now returns a 64-bit integer, which is composed of both the
    process id(high 32), and the RenderViewHost routing id(low 32). Also add a
    `GetProcessID` that retrieves the renderer process id, a requirement in some of
    our javascript code.

commit 524bab530acdff646fdc4d1168a450f621099268
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Sun Aug 21 07:30:21 2016 -0300

    Improve AtomSandboxedRendererClient to support preload scripts.
    
    Add RenderFrameObserver/RenderViewObserver subclasses that implement the
    necessary support for preload scripts in sandboxed renderers.

commit 72154b64ed6b26b8c76d3e54fbe49543615128a4
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Sun Aug 21 07:30:14 2016 -0300

    Embed setup bundle for preload scripts in sandboxed renderers.
    
    Add a gyp target that creates a browserify bundle starting with
    `lib/sandboxed_renderer/init.js`, which is embedded into the executable using
    the `atom_js2c` target.
    
    The goal of this bundle is to provide a very basic environment for preload
    scripts where a `require` function is available.
commit 695509e267b45992f6bc287bcf0851e48462cd38
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Sun Aug 21 07:22:52 2016 -0300

    Add browser side support for reading files from renderer.

commit 0f7652dc85b73d546272aa089525a29d607fb3e1
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Sat Aug 20 12:26:32 2016 -0300

    Move code that defines ipcRenderer methods into another file.

commit 1713200084084e6ec8ce058ae0e16566789a91d7
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Sat Aug 20 12:19:14 2016 -0300

    Refactor the atom_js2c target to include javascript from multiple dirs.
    
    Before invoking js2c, copy all files that must be embedded into the shared
    intermediate directory, and modify the js2c wrapper script to include all files
    from that directory(which is passed as argument).
    
    This allows the build system to embed files that don't share a common base
    directory, such as javascript generated at build time.

commit 6afe8aa7f2765cfcb2553651b0fc11dcce948eca
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Sat Aug 20 12:01:26 2016 -0300

    Move EmitIPCEvent into AtomRenderViewObserver.
    
    Refactor this function as a method so it is possible to inherit most behavior
    from AtomRenderViewObserver and override EmitIPCEvent.

commit c8629019f56929c0688cea94916fccf736487724
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Fri Aug 19 13:08:30 2016 -0300

    Expose atom_api_renderer_ipc module API

commit a7b6332ed0aa70c19d581b5792f2fdabc410dd4a
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Fri Aug 19 08:47:32 2016 -0300

    Add atom::AtomSandboxedRendererClient class

  Add atom::AtomSandboxedRendererClient class

commit 06cc9a44fe15aabb891ddaeeb90233b9977f374d
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Mon Aug 15 21:13:18 2016 -0300

    Add support for native chromium popups on sandboxed renderers.
    
    - Allow `api::Window` instances to be created from existing `api::WebContents`.
    - Override `WebContentsCreated` and `AddNewContents` to wrap renderer-created
      `content::WebContents` into `api::WebContents`.
    - For `content::WebContents` that should be displayed in new windows, pass the
      wrapped `api::WebContents` object to window manager.

commit 0b3b29938fc414222a197c62c50e11bb8f241c59
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Tue Aug 16 21:15:10 2016 -0300

    Allow api::WebContents to fully wrap an existing content::WebContents.
    
    - Add an overload to `WebContents::CreateFrom` that accepts a type parameter. If
      type is `REMOTE`, initialization is the same as before(a thin wrapper). If
      not, the `api::WebContents` will be fully initialized, as if it was created by
      `api::WebContents::Create`.
    - Move common initialization code to `InitWithSessionAndOptions`.

A list of the relevant commits

commit 1b1541fe1abda3c9059e34f682e86b7416342b2e
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Mon Aug 15 20:58:07 2016 -0300

    Decouple api::Window initialization from api::WebContents construction.

commit 1d228446db97f25c3754f38a1855a3c2b9af698d
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Mon Aug 15 08:01:24 2016 -0300

    Expose `--enable-sandbox` command-line switch.
    
    When `--enable-sandbox` is passed, electron will use chromium sandbox to spawn
    all renderers, and every new BrowserWindow will automatically have "sandboxed"
    passed as a web preference(since the renderer would not work properly
    otherwise).
commit c783ec72bcf84e0450ef8eb32c5f6d1b4eb53847
Author: Thiago de Arruda <tpadilha84@gmail.com>
Date:   Mon Aug 15 07:59:08 2016 -0300

    Add "sandboxed" option to "webPreferences".
    
    When "sandboxed" is passed as a web preference for `BrowserWindow`, the newly
    created renderer won't run any node.js code/integration, only communicating with
    the system via the IPC API of the content module. This is a requirement for
    running the renderer under chrome OS-level sandbox.
    
    Beyond that, certain behaviors of AtomBrowserClient are modified when dealing
    with sandboxed renderers:
    
    - `OverrideSiteInstanceNavigation` no longer create a new `SiteInstance` for
      every navigation. Instead, it reuses the source `SiteInstance` when not
      navigating to a different site.
    - `CanCreateWindow` will return true and allow javascript access.
```
