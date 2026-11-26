# GrassFX (gfx) Library Guide

This document lists all macros, type definitions, and GrassFX functions in the GrassFX library as reference tables. All examples and prototypes belong to the GrassFX abstraction layer.

## 1\. Macros (`#define`) and Type Definitions (`typedef`)

| Type | Return Type / Definition | Prototype / Expression | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- | :---- |
| **Typedef** | `typedef void (*Gfx_Callback)` | `typedef void (*Gfx_Callback)(void *data, Evas_Object *object, void *event_info);` | Defines the standard callback function signature. | \- `data`: User data (`void *`, can be `NULL`). \- `object`: Object triggering the event (`Evas_Object *`). \- `event_info`: Event-specific extra data (`void *`, can be `NULL`). |
| **Macro** | `void` | `#define GFX_CALLBACK(func_name) void func_name(void *data, Evas_Object *object, void *event_info)` | Shortens defining a callback function matching the `Gfx_Callback` signature. | \- `func_name`: Name of the function to be created (takes `void *data`, `Evas_Object *object`, `void *event_info` parameters). |

---

## 2\. General Functions (Initialization, Execution, Shutdown)

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `int` | `int gfx_init(int argc, char **argv);` | Initializes the library and underlying runtime. Returns a negative value on failure. | \- `argc`: Command-line argument count (`int`). \- `argv`: Command-line argument vector (`char **`). |
| `int` | `int gfx_run(void);` | Starts the main application loop and blocks until the window is closed. | None (`void`). Always returns `0`. |
| `int` | `int gfx_exit(void);` | Exits the running main loop. | None (`void`). Always returns `0`. |
| `int` | `int gfx_shutdown(void);` | Cleans up initialized resources and shuts down the library. | None (`void`). Returns the remaining initialization count (`int`). |

---

## 3\. Main Loop Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `void` | `void gfx_start_mainloop(void);` | Directly starts the low-level event loop. | None (`void`). |
| `double` | `double gfx_get_looptime(void);` | Returns the current timestamp of the main loop in seconds. | None (`void`). |

---

## 4\. Timer Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Ecore_Timer *` | `Ecore_Timer *gfx_add_timer(double in_seconds, Ecore_Task_Cb func, const void *data);` | Adds a timer triggered at the specified interval in seconds. Returns `NULL` if `func` is `NULL`. | \- `in_seconds`: Trigger interval (in seconds, `double`). \- `func`: Task callback function to call (`Ecore_Task_Cb`; returns `ECORE_CALLBACK_RENEW` or `ECORE_CALLBACK_CANCEL`). \- `data`: Data passed to the function (`const void *`, can be `NULL`). |
| `void` | `void gfx_del_timer(Ecore_Timer *timer);` | Deletes an existing timer. | \- `timer`: Timer pointer to delete (`Ecore_Timer *`). |
| `void` | `void gfx_timer_set_interval(Ecore_Timer *timer, double in_seconds);` | Updates the execution interval of a timer. | \- `timer`: Target timer (`Ecore_Timer *`). \- `in_seconds`: New interval duration (in seconds, `double`). |
| `double` | `double gfx_timer_get_interval(const Ecore_Timer *timer);` | Returns the current interval of a timer in seconds (`0.0` if `timer` is `NULL`). | \- `timer`: Timer to query (`const Ecore_Timer *`). |
| `void` | `void gfx_timer_delay(Ecore_Timer *timer, double add);` | Adds the specified duration to the next trigger time of a timer. | \- `timer`: Target timer (`Ecore_Timer *`). \- `add`: Delay duration to add (in seconds, `double`). |
| `void` | `void gfx_timer_freeze(Ecore_Timer *timer);` | Temporarily pauses (freezes) a timer. | \- `timer`: Timer to freeze (`Ecore_Timer *`). |
| `void` | `void gfx_timer_thaw(Ecore_Timer *timer);` | Resumes a frozen timer from where it left off. | \- `timer`: Timer to resume (`Ecore_Timer *`). |

---

## 5\. Idler and Poller Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Ecore_Idler *` | `Ecore_Idler *gfx_add_idler(Ecore_Task_Cb func, const void *data);` | Adds a task to run when the main loop is idle. Returns `NULL` if `func` is `NULL`. | \- `func`: Task function to call (`Ecore_Task_Cb`). \- `data`: Data passed to the function (`const void *`, can be `NULL`). |
| `void *` | `void *gfx_del_idler(Ecore_Idler *idler);` | Deletes an idler task and returns the user data passed at creation. | \- `idler`: Idler pointer to delete (`Ecore_Idler *`). |
| `Ecore_Poller *` | `Ecore_Poller *gfx_add_poller(Ecore_Poller_Type type, int interval, Ecore_Task_Cb func, const void *data);` | Adds a polling task that runs at periodic intervals. Returns `NULL` if `func` is `NULL`. | \- `type`: Poller type (`ECORE_POLLER_CORE`). \- `interval`: Trigger period (powers of 2 recommended, `int`). \- `func`: Function to call (`Ecore_Task_Cb`). \- `data`: User data (`const void *`, can be `NULL`). |
| `void *` | `void *gfx_del_poller(Ecore_Poller *poller);` | Deletes a poller task and returns its associated user data. | \- `poller`: Poller pointer to delete (`Ecore_Poller *`). |

---

## 6\. Thread Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Ecore_Thread *` | `Ecore_Thread *gfx_run_thread(Ecore_Thread_Cb func_blocking, Ecore_Thread_Cb func_end, Ecore_Thread_Cb func_cancel, const void *data);` | Starts a thread running a blocking operation in the background. Returns `NULL` if `func_blocking` is `NULL`. | \- `func_blocking`: Main function to run in the background (`Ecore_Thread_Cb`). \- `func_end`: Function called in the main loop on normal completion (`Ecore_Thread_Cb`, can be `NULL`). \- `func_cancel`: Function called on cancellation (`Ecore_Thread_Cb`, can be `NULL`). \- `data`: Shared data (`const void *`, can be `NULL`). |
| `Ecore_Thread *` | `Ecore_Thread *gfx_thread_run_feedback(Ecore_Thread_Cb func_heavy, Ecore_Thread_Notify_Cb func_notify, Ecore_Thread_Cb func_end, Ecore_Thread_Cb func_cancel, const void *data, Eina_Bool try_no_queue);` | Starts a thread capable of sending intermediate feedback notifications to the main loop while running. | \- `func_heavy`: Heavy operation running in the background (`Ecore_Thread_Cb`). \- `func_notify`: Function running in the main loop when a notification arrives (`Ecore_Thread_Notify_Cb`, can be `NULL`). \- `func_end`: Completion function (`Ecore_Thread_Cb`, can be `NULL`). \- `func_cancel`: Cancellation function (`Ecore_Thread_Cb`, can be `NULL`). \- `data`: User data (`const void *`, can be `NULL`). \- `try_no_queue`: Attempt direct execution without queuing (`EINA_TRUE` / `EINA_FALSE`). |
| `Eina_Bool` | `Eina_Bool gfx_thread_cancel(Ecore_Thread *th);` | Requests cancellation of a running thread. | \- `th`: Thread to cancel (`Ecore_Thread *`). Returns `EINA_TRUE` on success, `EINA_FALSE` otherwise. |
| `void` | `void gfx_thread_add_localdata(Ecore_Thread *th, const char *key, void *value, Eina_Free_Cb cb, Eina_Bool direct);` | Adds thread-local data as a key-value pair to a thread. | \- `th`: Target thread (`Ecore_Thread *`). \- `key`: Data key (`const char *`). \- `value`: Data pointer to store (`void *`). \- `cb`: Cleanup callback when freeing data (`Eina_Free_Cb`, can be `NULL`). \- `direct`: Direct assignment mode (`EINA_TRUE` / `EINA_FALSE`). |

---

## 7\. Process Execution Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Ecore_Exe *` | `Ecore_Exe *gfx_execute_run(const char *exe_command, const void *data);` | Launches an external command or program as a child process. | \- `exe_command`: Shell command to execute (`const char *`). \- `data`: User data associated with the process (`const void *`, can be `NULL`). |
| `Ecore_Exe *` | `Ecore_Exe *gfx_execute_run_cb(const char *exe_command, Ecore_Exe_Cb func, const void *data);` | Launches an external command and triggers callback `func` when the process exits. | \- `exe_command`: Command to execute (`const char *`). \- `func`: Function called when the process exits (`Ecore_Exe_Cb`). \- `data`: User data passed to the function (`const void *`, can be `NULL`). |
| `void` | `void gfx_execute_terminate(Ecore_Exe *exe);` | Sends a termination signal (`SIGTERM`) to a running child process. | \- `exe`: Target process pointer (`Ecore_Exe *`). |
| `void` | `void gfx_execute_kill(Ecore_Exe *exe);` | Forcefully kills a running child process (`SIGKILL`). | \- `exe`: Target process pointer (`Ecore_Exe *`). |
| `pid_t` | `pid_t gfx_execute_get_pid(const Ecore_Exe *exe);` | Returns the operating system process ID (PID) of the child process (`-1` if `exe` is `NULL`). | \- `exe`: Process to query (`const Ecore_Exe *`). |
| `void` | `void gfx_execute_free(Ecore_Exe *exe);` | Frees memory allocated for the process structure (does not kill the process). | \- `exe`: Process pointer to free (`Ecore_Exe *`). |

---

## 8\. File Descriptor (FD) and Event Handler Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Ecore_Fd_Handler *` | `Ecore_Fd_Handler *gfx_add_fdhandler(int fd, Ecore_Fd_Handler_Flags flags, Ecore_Fd_Cb func, const void *data, Ecore_Fd_Cb bufd_func, const void *bufd_data);` | Attaches a file descriptor (socket, pipe, etc.) to the main loop for read/write/error events. | \- `fd`: File descriptor to monitor (`int`, `>= 0`). \- `flags`: Monitoring flags (`ECORE_FD_READ`, `ECORE_FD_WRITE`, `ECORE_FD_ERROR`). \- `func`: Function called when an event occurs (`Ecore_Fd_Cb`). \- `data`: Data for `func` (`const void *`, can be `NULL`). \- `bufd_func`: Buffered data check function (`Ecore_Fd_Cb`, can be `NULL`). \- `bufd_data`: Data for `bufd_func` (`const void *`, can be `NULL`). |
| `void *` | `void *gfx_del_fdhandler(Ecore_Fd_Handler *fd_handler);` | Removes a file descriptor handler and returns its user data. | \- `fd_handler`: Handler to remove (`Ecore_Fd_Handler *`). |
| `Ecore_Event_Handler *` | `Ecore_Event_Handler *gfx_add_eventhandler(int type, Ecore_Event_Handler_Cb func, const void *data);` | Adds a handler for a specific system/application event type. | \- `type`: Event type ID (`int`, `>= 0`). \- `func`: Event handler function (`Ecore_Event_Handler_Cb`). \- `data`: Data passed to the function (`const void *`, can be `NULL`). |

---

## 9\. Canvas and Basic Object Management

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Evas *` | `Evas *gfx_object_get_evas(const Evas_Object *object);` | Returns the drawing canvas (`Evas *`) associated with the object. | \- `object`: Source object (`const Evas_Object *`). |
| `void` | `void gfx_object_show(Evas_Object *object);` | Makes the object visible on screen. | \- `object`: Object to show (`Evas_Object *`). |
| `void` | `void gfx_object_hide(Evas_Object *object);` | Hides the object from screen (does not delete it). | \- `object`: Object to hide (`Evas_Object *`). |
| `void` | `void gfx_object_del(Evas_Object *object);` | Deletes the object and frees its memory. | \- `object`: Object to delete (`Evas_Object *`). |
| `void` | `void gfx_object_raise(Evas_Object *object);` | Moves the object to the top of its drawing layer. | \- `object`: Object to raise (`Evas_Object *`). |
| `void` | `void gfx_object_lower(Evas_Object *object);` | Moves the object to the bottom of its drawing layer. | \- `object`: Object to lower (`Evas_Object *`). |
| `void` | `void gfx_object_set_size_hintweight(Evas_Object *object, double x, double y);` | Sets horizontal and vertical expansion weights of the object inside layout containers. | \- `object`: Target object (`Evas_Object *`). \- `x`: Horizontal weight (`0.0` fixed, `EVAS_HINT_EXPAND` / `1.0` expands). \- `y`: Vertical weight (`0.0` fixed, `EVAS_HINT_EXPAND` / `1.0` expands). |
| `void` | `void gfx_object_set_size_hintalign(Evas_Object *object, double x, double y);` | Sets alignment or fill behavior of the object within its allocated space. | \- `object`: Target object (`Evas_Object *`). \- `x`: Horizontal alignment (`0.0` left, `0.5` center, `1.0` right, `EVAS_HINT_FILL` / `-1.0` fill). \- `y`: Vertical alignment (`0.0` top, `0.5` middle, `1.0` bottom, `EVAS_HINT_FILL` / `-1.0` fill). |

---

## 10\. Geometry, Position, Color, and Clipping

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `void` | `void gfx_object_move(Evas_Object *object, Evas_Coord x, Evas_Coord y);` | Moves the object to the specified coordinates on the canvas. | \- `object`: Object to move (`Evas_Object *`). \- `x`: Horizontal position (pixels, `Evas_Coord`). \- `y`: Vertical position (pixels, `Evas_Coord`). |
| `void` | `void gfx_object_resize(Evas_Object *object, Evas_Coord w, Evas_Coord h);` | Changes the width and height of the object. | \- `object`: Object to resize (`Evas_Object *`). \- `w`: Width (pixels, `Evas_Coord`). \- `h`: Height (pixels, `Evas_Coord`). |
| `void` | `void gfx_object_get_geometry(Evas_Object *object, Evas_Coord *x, Evas_Coord *y, Evas_Coord *w, Evas_Coord *h);` | Retrieves the current position and size of the object. | \- `object`: Object to query (`Evas_Object *`). \- `x`, `y`, `w`, `h`: Output pointers (`Evas_Coord *`, unused ones can be `NULL`). |
| `void` | `void gfx_object_set_color(Evas_Object *object, int r, int g, int b, int a);` | Sets the RGBA color of the object (premultiplied alpha format). | \- `object`: Target object (`Evas_Object *`). \- `r`, `g`, `b`: Red, green, blue values (`0`–`255`). \- `a`: Alpha/opacity value (`0` fully transparent – `255` fully opaque). |
| `void` | `void gfx_object_get_color(const Evas_Object *object, int *r, int *g, int *b, int *a);` | Reads the current RGBA color components of the object. | \- `object`: Object to query (`const Evas_Object *`). \- `r`, `g`, `b`, `a`: Output pointers (`int *`, can be `NULL`). |
| `void` | `void gfx_object_set_minsize(Evas_Object *object, Evas_Coord w, Evas_Coord h);` | Sets the minimum size hint for the object. | \- `object`: Target object (`Evas_Object *`). \- `w`: Minimum width (`Evas_Coord`). \- `h`: Minimum height (`Evas_Coord`). |
| `void` | `void gfx_object_set_maxsize(Evas_Object *object, Evas_Coord w, Evas_Coord h);` | Sets the maximum size hint for the object. | \- `object`: Target object (`Evas_Object *`). \- `w`: Maximum width (`Evas_Coord`). \- `h`: Maximum height (`Evas_Coord`). |
| `void` | `void gfx_object_set_clip(Evas_Object *object, Evas_Object *clip);` | Clips (masks) the drawing of an object within another object's bounds. | \- `object`: Object to clip (`Evas_Object *`). \- `clip`: Object acting as the clip mask (`Evas_Object *`). |
| `void` | `void gfx_object_unset_clip(Evas_Object *object);` | Removes the clipping mask from an object. | \- `object`: Object to unclip (`Evas_Object *`). |

---

## 11\. Basic Canvas Objects (Primitives, Raw Image, Text, and Events)

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_rectangle(Evas *canvas);` | Adds a solid rectangle object to the canvas. | \- `canvas`: Target canvas (`Evas *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_rawimage(Evas *canvas);` | Adds a raw Evas image object to the canvas. | \- `canvas`: Target canvas (`Evas *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_text(Evas *canvas);` | Adds a single-line basic text object to the canvas. | \- `canvas`: Target canvas (`Evas *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_textblock(Evas *canvas);` | Adds a multi-line formatted textblock object to the canvas. | \- `canvas`: Target canvas (`Evas *`). |
| `void` | `void gfx_object_set_imagefile(Evas_Object *object, const char *file, const char *key);` | Loads an image file source into a raw image object. | \- `object`: Raw image object (`Evas_Object *`). \- `file`: Image file path (`const char *`). \- `key`: Group key inside archive/Eet (`const char *`, `NULL` for regular files). |
| `void` | `void gfx_object_set_imagefilled(Evas_Object *object, Eina_Bool filled);` | Sets whether a raw image automatically fills its object bounds. | \- `object`: Raw image object (`Evas_Object *`). \- `filled`: `EINA_TRUE` (auto-fill) or `EINA_FALSE`. |
| `void` | `void gfx_object_set_imagesize(Evas_Object *object, int w, int h);` | Sets the internal pixel dimensions of a raw image. | \- `object`: Raw image object (`Evas_Object *`). \- `w`, `h`: Pixel width and height (`int`). |
| `void` | `void gfx_object_get_imagesize(Evas_Object *object, int *w, int *h);` | Reads the internal pixel dimensions of a raw image. | \- `object`: Raw image object (`Evas_Object *`). \- `w`, `h`: Dimension output pointers (`int *`). |
| `Eina_Bool` | `Eina_Bool gfx_object_set_textfont(Evas_Object *object, const char *font, Evas_Font_Size size);` | Sets the font family and point size of a raw text object. | \- `object`: Text object (`Evas_Object *`). \- `font`: Font name (`const char *`, e.g., `"Sans"`). \- `size`: Font size (`Evas_Font_Size`, `int`). |
| `void` | `void gfx_object_set_rawtext(Evas_Object *object, const char *text);` | Updates the content of a raw text object. | \- `object`: Text object (`Evas_Object *`). \- `text`: Text string to set (`const char *`). |
| `const char *` | `const char *gfx_object_get_rawtext(const Evas_Object *object);` | Returns the current text of a raw text object. | \- `object`: Text object (`const Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_object_set_textblockmarkup(Evas_Object *object, const char *text);` | Sets markup-formatted text on a textblock object. | \- `object`: Textblock object (`Evas_Object *`). \- `text`: Formatted text (`const char *`). |
| `void` | `void gfx_object_add_eventcallback(Evas_Object *object, Evas_Callback_Type type, Evas_Object_Event_Cb func, const void *data);` | Attaches a low-level input/canvas event callback (mouse, keyboard, etc.) to an object. | \- `object`: Target object (`Evas_Object *`). \- `type`: Event type (`EVAS_CALLBACK_MOUSE_DOWN`, `EVAS_CALLBACK_KEY_DOWN`, etc.). \- `func`: Event callback function (`Evas_Object_Event_Cb`). \- `data`: User data (`const void *`, can be `NULL`). |
| `void` | `void gfx_object_add_smartcallback(Evas_Object *object, const char *event, Evas_Smart_Cb func, const void *data);` | Adds a high-level signal callback to a widget or smart object. | \- `object`: Target widget/object (`Evas_Object *`). \- `event`: Signal name (`const char *`, e.g., `"clicked"`, `"changed"`). \- `func`: Callback function (`Evas_Smart_Cb` or `Gfx_Callback`). \- `data`: User data (`const void *`, can be `NULL`). |
| `void` | `void gfx_object_set_focus(Evas_Object *object, Eina_Bool focus);` | Gives keyboard focus to or removes focus from an object. | \- `object`: Target object (`Evas_Object *`). \- `focus`: `EINA_TRUE` (focus) or `EINA_FALSE` (unfocus). |

---

## 12\. Window Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_new_feature_window(const char *name, const char *title);` | Creates a standard window with a default background. | \- `name`: Internal window name (`const char *`). \- `title`: Window title bar text (`const char *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_new_standard_window(Evas_Object *parent, const char *name, Elm_Win_Type type);` | Creates a base window object of the specified type. | \- `parent`: Parent window (`Evas_Object *`, `NULL` for main window). \- `name`: Internal window name (`const char *`). \- `type`: Window type (`ELM_WIN_BASIC`, `ELM_WIN_DIALOG_BASIC`, `ELM_WIN_SPLASH`, etc.). |
| `Evas_Object *` | `Evas_Object *gfx_object_create_window(Evas_Object *parent, const char *name, Elm_Win_Type type, const char *title, Evas_Coord x, Evas_Coord y);` | Creates a window, sets its title and autodel flag, resizes it, and shows it on screen. | \- `parent`: Parent window (`Evas_Object *`, can be `NULL`). \- `name`: Internal window name (`const char *`). \- `type`: Window type (`Elm_Win_Type`). \- `title`: Window title (`const char *`). \- `x`: Window width (`Evas_Coord`). \- `y`: Window height (`Evas_Coord`). |
| `int` | `int gfx_window_set_title(Evas_Object *object, const char *title);` | Changes the window title text. Returns `0` on success, `-1` on error. | \- `object`: Window object (`Evas_Object *`). \- `title`: New title text (`const char *`). |
| `int` | `int gfx_window_set_autodel(Evas_Object *object, Eina_Bool autodel);` | Sets whether the window is automatically deleted when closed (`0` / `-1`). | \- `object`: Window object (`Evas_Object *`). \- `autodel`: `EINA_TRUE` or `EINA_FALSE`. |
| `int` | `int gfx_window_set_center(Evas_Object *object, Eina_Bool h, Eina_Bool v);` | Centers the window horizontally and/or vertically on screen (`0` / `-1`). | \- `object`: Window object (`Evas_Object *`). \- `h`: Horizontal centering (`EINA_TRUE` / `EINA_FALSE`). \- `v`: Vertical centering (`EINA_TRUE` / `EINA_FALSE`). |
| `int` | `int gfx_window_set_minsize(Evas_Object *object, int w, int h);` | Sets the base/minimum size of the window (`0` / `-1`). | \- `object`: Window object (`Evas_Object *`). \- `w`: Width (`int`). \- `h`: Height (`int`). |
| `int` | `int gfx_window_set_fullscreen(Evas_Object *object, Eina_Bool full);` | Enables or disables fullscreen mode for the window (`0` / `-1`). | \- `object`: Window object (`Evas_Object *`). \- `full`: `EINA_TRUE` (fullscreen) or `EINA_FALSE`. |
| `int` | `int gfx_window_set_active(Evas_Object *object);` | Raises and activates the window (`0` / `-1`). | \- `object`: Window object (`Evas_Object *`). |
| `int` | `int gfx_window_set_iconified(Evas_Object *object, Eina_Bool iconified);` | Minimizes (iconifies) or restores the window (`0` / `-1`). | \- `object`: Window object (`Evas_Object *`). \- `iconified`: `EINA_TRUE` (minimize) or `EINA_FALSE`. |
| `int` | `int gfx_window_set_sticky(Evas_Object *object, Eina_Bool sticky);` | Sets whether the window appears across all virtual desktops (`0` / `-1`). | \- `object`: Window object (`Evas_Object *`). \- `sticky`: `EINA_TRUE` or `EINA_FALSE`. |
| `int` | `int gfx_window_proportion_scale(Evas_Object *object, Evas_Object *subobject);` | Adds a subobject to resize/scale automatically with the window (`0` / `-1`). | \- `object`: Window object (`Evas_Object *`). \- `subobject`: Container/widget attached to the window (`Evas_Object *`). |
| `void` | `void gfx_window_set_autohide(Evas_Object *object, Eina_Bool autohide);` | Sets whether the window hides automatically instead of being deleted on close. | \- `object`: Window object (`Evas_Object *`). \- `autohide`: `EINA_TRUE` or `EINA_FALSE`. |
| `void` | `void gfx_window_set_borderless(Evas_Object *object, Eina_Bool borderless);` | Hides or shows window borders and title bar. | \- `object`: Window object (`Evas_Object *`). \- `borderless`: `EINA_TRUE` (borderless) or `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_window_get_borderless(const Evas_Object *object);` | Returns whether the window is borderless. | \- `object`: Window object (`const Evas_Object *`). |
| `void` | `void gfx_window_set_shaped(Evas_Object *object, Eina_Bool shaped);` | Configures shaped (transparent mask) window mode. | \- `object`: Window object (`Evas_Object *`). \- `shaped`: `EINA_TRUE` or `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_window_get_shaped(const Evas_Object *object);` | Returns whether the window is in shaped mode. | \- `object`: Window object (`const Evas_Object *`). |
| `void` | `void gfx_window_set_alpha(Evas_Object *object, Eina_Bool alpha);` | Enables or disables alpha channel (translucent) support for the window. | \- `object`: Window object (`Evas_Object *`). \- `alpha`: `EINA_TRUE` or `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_window_get_alpha(const Evas_Object *object);` | Returns whether alpha channel support is enabled on the window. | \- `object`: Window object (`const Evas_Object *`). |

---

## 13\. UI Widgets (Widget Creation Functions)

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_button(Evas_Object *parent);` | Creates a clickable button widget. | \- `parent`: Parent object/window (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_checkbutton(Evas_Object *parent);` | Creates a checkbox widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_radiobutton(Evas_Object *parent);` | Creates a radio button widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_entry(Evas_Object *parent);` | Creates a text entry widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_slider(Evas_Object *parent);` | Creates a slider widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_spinner(Evas_Object *parent);` | Creates a numeric increment/decrement spinner widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_combobox(Evas_Object *parent);` | Creates a combobox dropdown widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_label(Evas_Object *parent);` | Creates a text label widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_icon(Evas_Object *parent);` | Creates a theme/standard icon widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_image(Evas_Object *parent);` | Creates a high-level image widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_progressbar(Evas_Object *parent);` | Creates a progress bar widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_bubble(Evas_Object *parent);` | Creates a bubble container widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_clock(Evas_Object *parent);` | Creates a digital clock widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_calendar(Evas_Object *parent);` | Creates a calendar widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_separator(Evas_Object *parent);` | Creates a divider separator widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_popup(Evas_Object *parent);` | Creates a popup alert/dialog widget. | \- `parent`: Parent object (`Evas_Object *`). |

---

## 14\. Common Object Content, Text, and State Management

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `void` | `void gfx_object_set_text(Evas_Object *object, const char *text);` | Sets the main text of a widget (button label, title, etc.). | \- `object`: Target widget (`Evas_Object *`). \- `text`: Text to assign (`const char *`). |
| `const char *` | `const char *gfx_object_get_text(const Evas_Object *object);` | Returns the main text of a widget. | \- `object`: Widget to query (`const Evas_Object *`). |
| `void` | `void gfx_object_set_content(Evas_Object *object, Evas_Object *content);` | Sets the main content object of a container widget. | \- `object`: Container widget (`Evas_Object *`). \- `content`: Content object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_get_content(const Evas_Object *object);` | Returns the current content object of a container widget. | \- `object`: Container widget (`const Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_image_set_file(Evas_Object *object, const char *file, const char *group);` | Loads an image file into an Elementary image widget. | \- `object`: Image widget (`Evas_Object *`). \- `file`: File path (`const char *`). \- `group`: Edje/Eet group name (`const char *`, can be `NULL`). |
| `Eina_Bool` | `Eina_Bool gfx_icon_set_standard(Evas_Object *object, const char *name);` | Assigns a standard theme icon to an icon widget. | \- `object`: Icon widget (`Evas_Object *`). \- `name`: Standard icon name (`const char *`, e.g., `"home"`, `"close"`). |
| `void` | `void gfx_object_set_parttext(Evas_Object *object, const char *part, const char *text);` | Sets text on a specific theme part of a widget. | \- `object`: Target widget (`Evas_Object *`). \- `part`: Part name (`const char *`, e.g., `"default"`, `"info"`). \- `text`: Text string (`const char *`). |
| `void` | `void gfx_object_set_partcontent(Evas_Object *object, const char *part, Evas_Object *content);` | Places a subobject into a specific theme part of a widget. | \- `object`: Target widget (`Evas_Object *`). \- `part`: Part name (`const char *`, e.g., `"icon"`). \- `content`: Object to place (`Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_set_policy(unsigned int policy, int value);` | Sets a global application policy (e.g., quit when last window closes). | \- `policy`: Policy type (e.g., `ELM_POLICY_QUIT`). \- `value`: Policy value (e.g., `ELM_POLICY_QUIT_LAST_WINDOW_CLOSED`). |
| `void` | `void gfx_object_set_disabled(Evas_Object *object, Eina_Bool disabled);` | Disables or enables user interaction on a widget. | \- `object`: Target widget (`Evas_Object *`). \- `disabled`: `EINA_TRUE` (disabled) or `EINA_FALSE` (enabled). |

---

## 15\. Entry, Check, Radio, and Slider Widget Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `void` | `void gfx_entry_set_text(Evas_Object *object, const char *text);` | Sets the text content of an entry widget. | \- `object`: Entry object (`Evas_Object *`). \- `text`: Text to write (`const char *`). |
| `const char *` | `const char *gfx_entry_get_text(const Evas_Object *object);` | Returns the text inside an entry widget. | \- `object`: Entry object (`const Evas_Object *`). |
| `void` | `void gfx_entry_set_singleline(Evas_Object *object, Eina_Bool single_line);` | Sets whether the entry operates in single-line or multi-line mode. | \- `object`: Entry object (`Evas_Object *`). \- `single_line`: `EINA_TRUE` (single-line) or `EINA_FALSE`. |
| `void` | `void gfx_entry_set_password(Evas_Object *object, Eina_Bool password);` | Enables or disables password mode (masking characters) on an entry. | \- `object`: Entry object (`Evas_Object *`). \- `password`: `EINA_TRUE` (password mode) or `EINA_FALSE`. |
| `void` | `void gfx_check_set_state(Evas_Object *object, Eina_Bool checked);` | Sets the checked state of a check widget. | \- `object`: Check object (`Evas_Object *`). \- `checked`: `EINA_TRUE` (checked) or `EINA_FALSE` (unchecked). |
| `Eina_Bool` | `Eina_Bool gfx_check_get_state(const Evas_Object *object);` | Returns whether a check widget is checked. | \- `object`: Check object (`const Evas_Object *`). |
| `void` | `void gfx_radio_set_statevalue(Evas_Object *object, int value);` | Sets the integer value represented by a radio button. | \- `object`: Radio object (`Evas_Object *`). \- `value`: Value assigned to this option (`int`). |
| `void` | `void gfx_radio_set_value(Evas_Object *object, int value);` | Changes the active selected value in a radio group. | \- `object`: Any radio object in the group (`Evas_Object *`). \- `value`: Radio value to select (`int`). |
| `int` | `int gfx_radio_get_value(const Evas_Object *object);` | Returns the currently selected value in a radio group (`-1` if `object` is `NULL`). | \- `object`: Radio object (`const Evas_Object *`). |
| `void` | `void gfx_radio_add_group(Evas_Object *object, Evas_Object *group);` | Adds a radio button to another radio button's group. | \- `object`: Radio to add to group (`Evas_Object *`). \- `group`: Main radio object of the target group (`Evas_Object *`). |
| `void` | `void gfx_radio_set_pointervalue(Evas_Object *object, int *valuep);` | Binds an external `int` variable that automatically updates with the selected radio value. | \- `object`: Radio object (`Evas_Object *`). \- `valuep`: Variable address (`int *`). |
| `void` | `void gfx_slider_set_minmax(Evas_Object *object, double min, double max);` | Sets the minimum and maximum bounds of a slider. | \- `object`: Slider object (`Evas_Object *`). \- `min`: Minimum value (`double`). \- `max`: Maximum value (`double`). |
| `void` | `void gfx_slider_set_unitformat(Evas_Object *object, const char *format);` | Sets the `printf`\-style unit label format on a slider. | \- `object`: Slider object (`Evas_Object *`). \- `format`: Format string (`const char *`, e.g., `"%1.0f %%"`). |
| `void` | `void gfx_slider_set_value(Evas_Object *object, double value);` | Sets the current value of a slider. | \- `object`: Slider object (`Evas_Object *`). \- `value`: New value (`double`). |
| `double` | `double gfx_slider_get_value(const Evas_Object *object);` | Returns the current value of a slider (`0.0` if `object` is `NULL`). | \- `object`: Slider object (`const Evas_Object *`). |

---

## 16\. Box Layout Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_box(Evas_Object *parent);` | Creates a box container arranging child objects horizontally or vertically. | \- `parent`: Parent object (`Evas_Object *`). |
| `void` | `void gfx_box_set_horizontal(Evas_Object *object, Eina_Bool horizontal);` | Sets the box layout direction to horizontal or vertical. | \- `object`: Box object (`Evas_Object *`). \- `horizontal`: `EINA_TRUE` (horizontal) or `EINA_FALSE` (vertical). |
| `Eina_Bool` | `Eina_Bool gfx_box_get_horizontal(const Evas_Object *object);` | Returns whether the box is in horizontal mode. | \- `object`: Box object (`const Evas_Object *`). |
| `void` | `void gfx_box_pack_start(Evas_Object *object, Evas_Object *child);` | Packs a child object at the beginning of the box. | \- `object`: Box object (`Evas_Object *`). \- `child`: Object to pack (`Evas_Object *`). |
| `void` | `void gfx_box_pack_end(Evas_Object *object, Evas_Object *child);` | Packs a child object at the end of the box. | \- `object`: Box object (`Evas_Object *`). \- `child`: Object to pack (`Evas_Object *`). |
| `void` | `void gfx_box_pack_before(Evas_Object *object, Evas_Object *child, Evas_Object *before);` | Packs a child object immediately before a reference object. | \- `object`: Box object (`Evas_Object *`). \- `child`: Object to pack (`Evas_Object *`). \- `before`: Reference object (`Evas_Object *`). |
| `void` | `void gfx_box_pack_after(Evas_Object *object, Evas_Object *child, Evas_Object *after);` | Packs a child object immediately after a reference object. | \- `object`: Box object (`Evas_Object *`). \- `child`: Object to pack (`Evas_Object *`). \- `after`: Reference object (`Evas_Object *`). |
| `void` | `void gfx_box_unpack(Evas_Object *object, Evas_Object *child);` | Unpacks a specific child object from the box (does not delete it). | \- `object`: Box object (`Evas_Object *`). \- `child`: Object to unpack (`Evas_Object *`). |
| `void` | `void gfx_box_unpack_all(Evas_Object *object);` | Unpacks all child objects from the box (does not delete them). | \- `object`: Box object (`Evas_Object *`). |
| `void` | `void gfx_box_set_homogeneous(Evas_Object *object, Eina_Bool homogeneous);` | Sets whether all items in the box receive equal space. | \- `object`: Box object (`Evas_Object *`). \- `homogeneous`: `EINA_TRUE` or `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_box_get_homogeneous(const Evas_Object *object);` | Returns whether the box is in homogeneous mode. | \- `object`: Box object (`const Evas_Object *`). |
| `void` | `void gfx_box_set_padding(Evas_Object *object, Evas_Coord horizontal, Evas_Coord vertical);` | Sets horizontal and vertical spacing (in pixels) between items in the box. | \- `object`: Box object (`Evas_Object *`). \- `horizontal`, `vertical`: Spacing values (`Evas_Coord`). |
| `void` | `void gfx_box_get_padding(Evas_Object *object, Evas_Coord *horizontal, Evas_Coord *vertical);` | Reads the item spacing values of the box. | \- `object`: Box object (`Evas_Object *`). \- `horizontal`, `vertical`: Output pointers (`Evas_Coord *`). |
| `void` | `void gfx_box_set_align(Evas_Object *object, double horizontal, double vertical);` | Sets overall alignment of items inside the box. | \- `object`: Box object (`Evas_Object *`). \- `horizontal`, `vertical`: Alignment ratio (`0.0`–`1.0`). |
| `void` | `void gfx_box_get_align(Evas_Object *object, double *horizontal, double *vertical);` | Reads the overall alignment values of the box. | \- `object`: Box object (`Evas_Object *`). \- `horizontal`, `vertical`: Output pointers (`double *`). |
| `Eina_List *` | `Eina_List *gfx_box_get_children(const Evas_Object *object);` | Returns a list of child objects inside the box. | \- `object`: Box object (`const Evas_Object *`). |
| `void` | `void gfx_box_clear(Evas_Object *object);` | Unpacks and deletes all child objects in the box. | \- `object`: Box object (`Evas_Object *`). |
| `void` | `void gfx_box_set_layout(Evas_Object *object, Evas_Object_Box_Layout func, const void *data, Eina_Free_Cb free_data);` | Assigns a custom layout algorithm to the box. | \- `object`: Box object (`Evas_Object *`). \- `func`: Layout function (`Evas_Object_Box_Layout`). \- `data`: Layout data (`const void *`, can be `NULL`). \- `free_data`: Data destructor (`Eina_Free_Cb`, can be `NULL`). |

---

## 17\. Grid and Table Layout Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_grid(Evas_Object *parent);` | Creates a grid container based on a virtual coordinate system. | \- `parent`: Parent object (`Evas_Object *`). |
| `void` | `void gfx_grid_set_size(Evas_Object *object, int w, int h);` | Sets virtual column and row resolution of the grid (default `100x100`). | \- `object`: Grid object (`Evas_Object *`). \- `w`, `h`: Virtual width and height units (`int`). |
| `void` | `void gfx_grid_get_size(const Evas_Object *object, int *w, int *h);` | Reads virtual dimensions of the grid. | \- `object`: Grid object (`const Evas_Object *`). \- `w`, `h`: Output pointers (`int *`). |
| `void` | `void gfx_grid_add_pack(Evas_Object *object, Evas_Object *subobject, int x, int y, int w, int h);` | Packs a subobject at virtual coordinates and size on the grid. | \- `object`: Grid object (`Evas_Object *`). \- `subobject`: Object to add (`Evas_Object *`). \- `x`, `y`: Starting cell (`int`). \- `w`, `h`: Spanned cell width and height (`int`). |
| `void` | `void gfx_grid_unpack(Evas_Object *object, Evas_Object *subobject);` | Unpacks a subobject from the grid (does not delete it). | \- `object`: Grid object (`Evas_Object *`). \- `subobject`: Object to unpack (`Evas_Object *`). |
| `void` | `void gfx_grid_set_pack(Evas_Object *subobject, Evas_Coord x, Evas_Coord y, Evas_Coord w, Evas_Coord h);` | Updates position and size of a subobject already packed in a grid. | \- `subobject`: Subobject inside grid (`Evas_Object *`). \- `x`, `y`, `w`, `h`: New virtual coordinates and sizes (`Evas_Coord`). |
| `void` | `void gfx_grid_get_pack(Evas_Object *subobject, Evas_Coord *x, Evas_Coord *y, Evas_Coord *w, Evas_Coord *h);` | Reads position and size of a subobject on the grid. | \- `subobject`: Subobject inside grid (`Evas_Object *`). \- `x`, `y`, `w`, `h`: Output pointers (`Evas_Coord *`). |
| `Eina_List *` | `Eina_List *gfx_grid_get_children(const Evas_Object *object);` | Returns a list of all subobjects in the grid. | \- `object`: Grid object (`const Evas_Object *`). |
| `void` | `void gfx_grid_clear(Evas_Object *object, Eina_Bool clear);` | Clears the grid; deletes subobjects if `clear` is `EINA_TRUE`. | \- `object`: Grid object (`Evas_Object *`). \- `clear`: `EINA_TRUE` (delete objects) or `EINA_FALSE` (unpack only). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_table(Evas_Object *parent);` | Creates a table container made of rows and columns. | \- `parent`: Parent object (`Evas_Object *`). |
| `void` | `void gfx_table_set_align(Evas_Object *object, double horizontal, double vertical);` | Sets alignment of table content. | \- `object`: Table object (`Evas_Object *`). \- `horizontal`, `vertical`: Alignment ratio (`0.0`–`1.0`). |
| `void` | `void gfx_table_get_align(const Evas_Object *object, double *horizontal, double *vertical);` | Reads table alignment values. | \- `object`: Table object (`const Evas_Object *`). \- `horizontal`, `vertical`: Output pointers (`double *`). |
| `Evas_Object *` | `Evas_Object *gfx_table_get_child(const Evas_Object *object, int col, int row);` | Returns the subobject at the specified column and row. | \- `object`: Table object (`const Evas_Object *`). \- `col`: Column index (`int`). \- `row`: Row index (`int`). |
| `void` | `void gfx_table_clear(Evas_Object *object, Eina_Bool clear);` | Clears the table; deletes subobjects if `clear` is `EINA_TRUE`. | \- `object`: Table object (`Evas_Object *`). \- `clear`: `EINA_TRUE` or `EINA_FALSE`. |
| `void` | `void gfx_table_set_homogeneous(Evas_Object *object, Eina_Bool homogeneous);` | Sets whether table cells have equal size. | \- `object`: Table object (`Evas_Object *`). \- `homogeneous`: `EINA_TRUE` or `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_table_get_homogeneous(const Evas_Object *object);` | Returns whether the table is in homogeneous mode. | \- `object`: Table object (`const Evas_Object *`). |
| `void` | `void gfx_table_pack(Evas_Object *object, Evas_Object *subobject, int col, int row, int colspan, int rowspan);` | Places a subobject into the specified cell of the table. | \- `object`: Table object (`Evas_Object *`). \- `subobject`: Object to add (`Evas_Object *`). \- `col`, `row`: Column and row position (`int`). \- `colspan`, `rowspan`: Number of columns and rows spanned (`int`, at least `1`). |
| `void` | `void gfx_table_set_pack(Evas_Object *subobject, int col, int row, int colspan, int rowspan);` | Updates cell position and span of a subobject in the table. | \- `subobject`: Subobject inside table (`Evas_Object *`). \- `col`, `row`, `colspan`, `rowspan`: New cell values (`int`). |
| `void` | `void gfx_table_get_pack(Evas_Object *subobject, int *col, int *row, int *colspan, int *rowspan);` | Reads cell information of a subobject in the table. | \- `subobject`: Subobject inside table (`Evas_Object *`). \- `col`, `row`, `colspan`, `rowspan`: Output pointers (`int *`). |
| `void` | `void gfx_table_set_padding(Evas_Object *object, Evas_Coord horizontal, Evas_Coord vertical);` | Sets horizontal and vertical padding between table cells. | \- `object`: Table object (`Evas_Object *`). \- `horizontal`, `vertical`: Padding (pixels, `Evas_Coord`). |
| `void` | `void gfx_table_get_padding(Evas_Object *object, Evas_Coord *horizontal, Evas_Coord *vertical);` | Reads table cell padding values. | \- `object`: Table object (`Evas_Object *`). \- `horizontal`, `vertical`: Output pointers (`Evas_Coord *`). |
| `void` | `void gfx_table_unpack(Evas_Object *object, Evas_Object *subobject);` | Unpacks a subobject from the table (does not delete it). | \- `object`: Table object (`Evas_Object *`). \- `subobject`: Object to unpack (`Evas_Object *`). |

---

## 18\. Scroller and Frame Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_scroller(Evas_Object *parent);` | Creates a scrollable container widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `void` | `void gfx_scroller_set_policy(Evas_Object *object, Elm_Scroller_Policy policy_h, Elm_Scroller_Policy policy_v);` | Sets visibility policy for horizontal and vertical scrollbars. | \- `object`: Scroller object (`Evas_Object *`). \- `policy_h`, `policy_v`: `ELM_SCROLLER_POLICY_AUTO`, `ELM_SCROLLER_POLICY_ON`, `ELM_SCROLLER_POLICY_OFF`. |
| `void` | `void gfx_scroller_get_policy(const Evas_Object *object, Elm_Scroller_Policy *policy_h, Elm_Scroller_Policy *policy_v);` | Reads current scrollbar policies. | \- `object`: Scroller object (`const Evas_Object *`). \- `policy_h`, `policy_v`: Output pointers (`Elm_Scroller_Policy *`). |
| `void` | `void gfx_scroller_set_region(Evas_Object *object, Evas_Coord x, Evas_Coord y, Evas_Coord w, Evas_Coord h);` | Scrolls the specified rectangular region into the visible area. | \- `object`: Scroller object (`Evas_Object *`). \- `x`, `y`, `w`, `h`: Region coordinates and dimensions (`Evas_Coord`). |
| `void` | `void gfx_scroller_get_region(const Evas_Object *object, Evas_Coord *x, Evas_Coord *y, Evas_Coord *w, Evas_Coord *h);` | Reads the currently visible scroll region. | \- `object`: Scroller object (`const Evas_Object *`). \- `x`, `y`, `w`, `h`: Output pointers (`Evas_Coord *`). |
| `void` | `void gfx_scroller_get_position(const Evas_Object *object, Evas_Coord *x, Evas_Coord *y);` | Reads the current scroll position (`x`, `y`). | \- `object`: Scroller object (`const Evas_Object *`). \- `x`, `y`: Position output pointers (`Evas_Coord *`). |
| `void` | `void gfx_scroller_set_pagesize(Evas_Object *object, Evas_Coord w, Evas_Coord h);` | Sets the page dimensions of the scroller. | \- `object`: Scroller object (`Evas_Object *`). \- `w`, `h`: Page width and height (`Evas_Coord`). |
| `void` | `void gfx_scroller_get_pagesize(const Evas_Object *object, Evas_Coord *w, Evas_Coord *h);` | Reads the page dimensions of the scroller. | \- `object`: Scroller object (`const Evas_Object *`). \- `w`, `h`: Output pointers (`Evas_Coord *`). |
| `void` | `void gfx_scroller_get_currentpage(const Evas_Object *object, int *hp, int *vp);` | Reads the current horizontal and vertical page indices. | \- `object`: Scroller object (`const Evas_Object *`). \- `hp`, `vp`: Horizontal and vertical page output pointers (`int *`). |
| `void` | `void gfx_scroller_show_page(Evas_Object *object, int hp, int vp);` | Scrolls to the specified horizontal and vertical page. | \- `object`: Scroller object (`Evas_Object *`). \- `hp`, `vp`: Target horizontal and vertical page indices (`int`). |
| `void` | `void gfx_scroller_set_bounce(Evas_Object *object, Eina_Bool h_bounce, Eina_Bool v_bounce);` | Enables or disables edge bounce effect on horizontal and vertical scrolling. | \- `object`: Scroller object (`Evas_Object *`). \- `h_bounce`, `v_bounce`: `EINA_TRUE` or `EINA_FALSE`. |
| `void` | `void gfx_scroller_get_bounce(const Evas_Object *object, Eina_Bool *h_bounce, Eina_Bool *v_bounce);` | Reads whether horizontal and vertical edge bounce effects are enabled. | \- `object`: Scroller object (`const Evas_Object *`). \- `h_bounce`, `v_bounce`: Output pointers (`Eina_Bool *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_frame(Evas_Object *parent);` | Creates a titled frame container widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `void` | `void gfx_frame_set_autocollapse(Evas_Object *object, Eina_Bool autocollapse);` | Sets whether the frame automatically collapses/expands when clicked. | \- `object`: Frame object (`Evas_Object *`). \- `autocollapse`: `EINA_TRUE` or `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_frame_get_autocollapse(const Evas_Object *object);` | Returns whether autocollapse is enabled on the frame. | \- `object`: Frame object (`const Evas_Object *`). |
| `void` | `void gfx_frame_set_collapse(Evas_Object *object, Eina_Bool collapse);` | Collapses or expands the frame content. | \- `object`: Frame object (`Evas_Object *`). \- `collapse`: `EINA_TRUE` (collapsed) or `EINA_FALSE` (expanded). |
| `Eina_Bool` | `Eina_Bool gfx_frame_get_collapse(const Evas_Object *object);` | Returns whether the frame is currently collapsed. | \- `object`: Frame object (`const Evas_Object *`). |

---

## 19\. Genlist and Background Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Elm_Object_Item *` | `Elm_Object_Item *gfx_genlist_add_item(Evas_Object *object, const Elm_Genlist_Item_Class *itemclass, const void *data, Elm_Object_Item *parent, Elm_Genlist_Item_Type flags, Evas_Smart_Cb function, const void *function_data);` | Appends a new item to a genlist widget. | \- `object`: Genlist object (`Evas_Object *`). \- `itemclass`: Item class definition (`const Elm_Genlist_Item_Class *`). \- `data`: Item user data (`const void *`, can be `NULL`). \- `parent`: Parent item for tree items (`Elm_Object_Item *`, `NULL` for top-level). \- `flags`: Item type (`ELM_GENLIST_ITEM_NONE`, `ELM_GENLIST_ITEM_TREE`, etc.). \- `function`: Selection callback (`Evas_Smart_Cb`, can be `NULL`). \- `function_data`: Callback user data (`const void *`, can be `NULL`). |
| `void` | `void gfx_genlist_clear(Evas_Object *object);` | Removes all items from a genlist widget. | \- `object`: Genlist object (`Evas_Object *`). |
| `Elm_Genlist_Item_Class *` | `Elm_Genlist_Item_Class *gfx_genlist_add_itemclass(void);` | Allocates and returns a new genlist item class structure. | None (`void`). |
| `void` | `void gfx_genlist_free_itemclass(Elm_Genlist_Item_Class *itemclass);` | Frees a previously allocated genlist item class structure. | \- `itemclass`: Item class pointer (`Elm_Genlist_Item_Class *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_background(Evas_Object *parent);` | Creates a background (bg) object. | \- `parent`: Parent object (`Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_background_set_file(Evas_Object *object, const char *file, const char *group);` | Loads an image or Edje file as the background source. | \- `object`: Background object (`Evas_Object *`). \- `file`: File path (`const char *`). \- `group`: Group name inside file (`const char *`, can be `NULL`). |
| `void` | `void gfx_background_get_file(const Evas_Object *object, const char **file, const char **group);` | Reads the current file path and group name of the background. | \- `object`: Background object (`const Evas_Object *`). \- `file`, `group`: Output pointers (`const char **`). |
| `void` | `void gfx_background_set_option(Evas_Object *object, Elm_Bg_Option option);` | Sets how the background image is displayed/scaled. | \- `object`: Background object (`Evas_Object *`). \- `option`: `ELM_BG_OPTION_CENTER`, `ELM_BG_OPTION_SCALE`, `ELM_BG_OPTION_TILE`, `ELM_BG_OPTION_STRETCH`. |
| `Elm_Bg_Option` | `Elm_Bg_Option gfx_background_get_option(const Evas_Object *object);` | Returns the current background display option. | \- `object`: Background object (`const Evas_Object *`). |
| `void` | `void gfx_background_set_color(Evas_Object *object, int r, int g, int b);` | Sets the solid RGB color of the background. | \- `object`: Background object (`Evas_Object *`). \- `r`, `g`, `b`: Red, green, blue values (`0`–`255`). |
| `void` | `void gfx_background_get_color(const Evas_Object *object, int *r, int *g, int *b);` | Reads the current RGB color components of the background. | \- `object`: Background object (`const Evas_Object *`). \- `r`, `g`, `b`: Output pointers (`int *`). |
| `void` | `void gfx_background_set_loadsize(Evas_Object *object, int w, int h);` | Sets the load/decode resolution hint for the background image. | \- `object`: Background object (`Evas_Object *`). \- `w`, `h`: Load width and height (`int`). |

---

## 20\. Menu Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_menu(Evas_Object *parent);` | Creates a menu widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Elm_Object_Item *` | `Elm_Object_Item *gfx_menu_add_item(Evas_Object *object, Elm_Object_Item *parent, const char *icon, const char *label, Evas_Smart_Cb func, const void *data);` | Adds a new item to a menu or under a parent menu item. | \- `object`: Menu object (`Evas_Object *`). \- `parent`: Parent item (`Elm_Object_Item *`, `NULL` for top-level). \- `icon`: Icon name (`const char *`, can be `NULL`). \- `label`: Item label text (`const char *`). \- `func`: Selection callback (`Evas_Smart_Cb`, can be `NULL`). \- `data`: Callback user data (`const void *`, can be `NULL`). |
| `Elm_Object_Item *` | `Elm_Object_Item *gfx_menu_add_separator(Evas_Object *object, Elm_Object_Item *parent);` | Adds a separator line item to a menu. | \- `object`: Menu object (`Evas_Object *`). \- `parent`: Parent item (`Elm_Object_Item *`, `NULL` for top-level). |
| `Elm_Object_Item *` | `Elm_Object_Item *gfx_menu_add_submenu(Evas_Object *object, Elm_Object_Item *parent, const char *label);` | Adds a submenu container item under a menu or parent item. | \- `object`: Menu object (`Evas_Object *`). \- `parent`: Parent item (`Elm_Object_Item *`, `NULL` for top-level). \- `label`: Submenu label text (`const char *`). |
| `void` | `void gfx_menu_move(Evas_Object *object, Evas_Coord x, Evas_Coord y);` | Moves the menu to the specified coordinates. | \- `object`: Menu object (`Evas_Object *`). \- `x`, `y`: Target coordinates (`Evas_Coord`). |
| `void` | `void gfx_menu_close(Evas_Object *object);` | Closes an open menu. | \- `object`: Menu object (`Evas_Object *`). |
| `Elm_Object_Item *` | `Elm_Object_Item *gfx_menu_get_selected(const Evas_Object *object);` | Returns the currently selected menu item. | \- `object`: Menu object (`const Evas_Object *`). |
| `const Eina_List *` | `const Eina_List *gfx_menu_get_items(const Evas_Object *object);` | Returns the list of top-level menu items. | \- `object`: Menu object (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_menu_get_itemobject(const Elm_Object_Item *item);` | Returns the widget associated with a menu item. | \- `item`: Menu item (`const Elm_Object_Item *`). |
| `void` | `void gfx_menu_set_itemselected(Elm_Object_Item *item, Eina_Bool selected);` | Sets whether a menu item is selected. | \- `item`: Menu item (`Elm_Object_Item *`). \- `selected`: `EINA_TRUE` or `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_menu_get_itemdisabled(const Elm_Object_Item *item);` | Returns whether a menu item is disabled. | \- `item`: Menu item (`const Elm_Object_Item *`). |
| `void` | `void gfx_menu_set_itemicon(Elm_Object_Item *item, const char *icon);` | Sets the icon name of a menu item. | \- `item`: Menu item (`Elm_Object_Item *`). \- `icon`: Icon name (`const char *`). |
| `const char *` | `const char *gfx_menu_get_itemicon(const Elm_Object_Item *item);` | Returns the icon name of a menu item. | \- `item`: Menu item (`const Elm_Object_Item *`). |
| `void` | `void gfx_menu_set_itemlabel(Elm_Object_Item *item, const char *label);` | Sets the label text of a menu item. | \- `item`: Menu item (`Elm_Object_Item *`). \- `label`: Label text (`const char *`). |
| `const char *` | `const char *gfx_menu_get_itemlabel(const Elm_Object_Item *item);` | Returns the label text of a menu item. | \- `item`: Menu item (`const Elm_Object_Item *`). |
| `Eina_Bool` | `Eina_Bool gfx_menu_get_itemselected(const Elm_Object_Item *item);` | Returns whether a menu item is selected. | \- `item`: Menu item (`const Elm_Object_Item *`). |
| `const Eina_List *` | `const Eina_List *gfx_menu_get_subitems(const Elm_Object_Item *item);` | Returns the list of subitems under a menu item. | \- `item`: Menu item (`const Elm_Object_Item *`). |
| `void` | `void gfx_menu_del_item(Elm_Object_Item *item);` | Deletes a menu item. | \- `item`: Menu item (`Elm_Object_Item *`). |

---

## 21\. Flip, Hover, Inwin, Layout, Map, Panel, Panes, and Photocam Functions

| Return Type | Function Prototype | Function | Parameters and Allowed Values |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_flip(Evas_Object *parent);` | Creates a flip container widget for animated front/back transitions. | \- `parent`: Parent object (`Evas_Object *`). |
| `void` | `void gfx_flip_go(Evas_Object *object, Elm_Flip_Mode mode);` | Triggers a flip transition using the specified animation mode. | \- `object`: Flip object (`Evas_Object *`). \- `mode`: Flip animation mode (`ELM_FLIP_ROTATE_XZ_CENTER_AXIS`, `ELM_FLIP_CUBE_LEFT`, `ELM_FLIP_PAGE_LEFT`, etc.). |
| `void` | `void gfx_flip_go_to(Evas_Object *object, Eina_Bool front, Elm_Flip_Mode mode);` | Transitions directly to the front or back side using the specified mode. | \- `object`: Flip object (`Evas_Object *`). \- `front`: `EINA_TRUE` (front) or `EINA_FALSE` (back). \- `mode`: Flip mode (`Elm_Flip_Mode`). |
| `void` | `void gfx_flip_set_interaction(Evas_Object *object, Elm_Flip_Interaction mode);` | Sets user interaction mode (e.g., swipe/drag) on a flip widget. | \- `object`: Flip object (`Evas_Object *`). \- `mode`: `ELM_FLIP_INTERACTION_NONE`, `ELM_FLIP_INTERACTION_ROTATE`, `ELM_FLIP_INTERACTION_CUBE`, `ELM_FLIP_INTERACTION_PAGE`. |
| `void` | `void gfx_flip_set_swipedirection(Evas_Object *object, Elm_Flip_Direction dir, Eina_Bool enabled);` | Enables or disables swipe interaction in a given direction. | \- `object`: Flip object (`Evas_Object *`). \- `dir`: `ELM_FLIP_DIRECTION_UP`, `ELM_FLIP_DIRECTION_DOWN`, `ELM_FLIP_DIRECTION_LEFT`, `ELM_FLIP_DIRECTION_RIGHT`. \- `enabled`: `EINA_TRUE` or `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_flip_get_swipedirection(Evas_Object *object, Elm_Flip_Direction dir);` | Returns whether swipe interaction is enabled in the given direction. | \- `object`: Flip object (`Evas_Object *`). \- `dir`: Flip direction (`Elm_Flip_Direction`). |
| `void` | `void gfx_flip_set_swipehitsize(Evas_Object *object, Elm_Flip_Direction dir, double siz);` | Sets the swipe hit area size ratio (`0.0`–`1.0`) for a direction. | \- `object`: Flip object (`Evas_Object *`). \- `dir`: Flip direction (`Elm_Flip_Direction`). \- `siz`: Hit size ratio (`double`, `0.0`–`1.0`). |
| `double` | `double gfx_flip_get_swipehitsize(Evas_Object *object, Elm_Flip_Direction dir);` | Returns the swipe hit area size ratio for a direction. | \- `object`: Flip object (`Evas_Object *`). \- `dir`: Flip direction (`Elm_Flip_Direction`). |
| `void` | `void gfx_flip_set_perspective(Evas_Object *object, Evas_Coord foc, Evas_Coord x, Evas_Coord y);` | Sets focal length and vanishing point coordinates for flip perspective. | \- `object`: Flip object (`Evas_Object *`). \- `foc`: Focal length (`Evas_Coord`). \- `x`, `y`: Vanishing point coordinates (`Evas_Coord`). |
| `Evas_Object *` | `Evas_Object *gfx_flip_get_selectedchild(const Evas_Object *object);` | Returns the content object currently set on the `"front"` part of the flip. | \- `object`: Flip object (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_hover(Evas_Object *parent);` | Creates a hover popup widget anchored to a target object. | \- `parent`: Parent object (`Evas_Object *`). |
| `void` | `void gfx_hover_set_target(Evas_Object *object, Evas_Object *target);` | Sets the target object that the hover widget attaches to. | \- `object`: Hover object (`Evas_Object *`). \- `target`: Target object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_hover_get_target(const Evas_Object *object);` | Returns the target object of a hover widget. | \- `object`: Hover object (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_inwin(Evas_Object *parent);` | Creates an in-window (inwin) overlay dialog container. | \- `parent`: Parent window (`Evas_Object *`). |
| `void` | `void gfx_inwin_set_content(Evas_Object *object, Evas_Object *content);` | Sets the content object displayed inside an inwin container. | \- `object`: Inwin object (`Evas_Object *`). \- `content`: Content object (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_inwin_get_content(const Evas_Object *object);` | Returns the content object inside an inwin container. | \- `object`: Inwin object (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_layout(Evas_Object *parent);` | Creates an Edje/theme layout container widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_layout_set_file(Evas_Object *object, const char *file, const char *group);` | Loads an Edje file and group into a layout widget. | \- `object`: Layout object (`Evas_Object *`). \- `file`: Edje file path (`const char *`). \- `group`: Group name (`const char *`). |
| `void` | `void gfx_layout_get_file(Evas_Object *object, const char **file, const char **group);` | Reads the file path and group name loaded in a layout widget. | \- `object`: Layout object (`Evas_Object *`). \- `file`, `group`: Output pointers (`const char **`). |
| `Eina_Bool` | `Eina_Bool gfx_layout_set_theme(Evas_Object *object, const char *klass, const char *group, const char *style);` | Applies a theme class, group, and style to a layout widget. | \- `object`: Layout object (`Evas_Object *`). \- `klass`: Theme class (`const char *`). \- `group`: Theme group (`const char *`). \- `style`: Theme style (`const char *`). |
| `void` | `void gfx_layout_signal_emit(Evas_Object *object, const char *emission, const char *source);` | Emits a signal to the layout's underlying Edje object. | \- `object`: Layout object (`Evas_Object *`). \- `emission`: Signal name (`const char *`). \- `source`: Signal source (`const char *`). |
| `void` | `void gfx_layout_add_signalcallback(Evas_Object *object, const char *emission, const char *source, Edje_Signal_Cb func, const void *data);` | Registers a callback for a signal emitted by the layout. | \- `object`: Layout object (`Evas_Object *`). \- `emission`: Signal name pattern (`const char *`). \- `source`: Source pattern (`const char *`). \- `func`: Signal callback (`Edje_Signal_Cb`). \- `data`: User data (`const void *`, can be `NULL`). |
| `void` | `void gfx_layout_del_signalcallback(Evas_Object *object, const char *emission, const char *source, Edje_Signal_Cb func);` | Removes a previously registered signal callback from a layout. | \- `object`: Layout object (`Evas_Object *`). \- `emission`: Signal name pattern (`const char *`). \- `source`: Source pattern (`const char *`). \- `func`: Signal callback (`Edje_Signal_Cb`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_map(Evas_Object *parent);` | Creates an interactive map widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `void` | `void gfx_map_set_zoom(Evas_Object *object, int zoom);` | Sets the zoom level of a map widget. | \- `object`: Map object (`Evas_Object *`). \- `zoom`: Zoom level (`int`). |
| `int` | `int gfx_map_get_zoom(const Evas_Object *object);` | Returns the current zoom level of a map widget. | \- `object`: Map object (`const Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_map_set_source(Evas_Object *object, const char *type, const char *source);` | Sets the map source provider for the given source type. | \- `object`: Map object (`Evas_Object *`). \- `type`: Source type (`"tile"`, `"route"`, or `"name"`, `const char *`). \- `source`: Source name (`const char *`). |
| `const char *` | `const char *gfx_map_get_source(const Evas_Object *object, const char *type);` | Returns the current source name for the given map source type. | \- `object`: Map object (`const Evas_Object *`). \- `type`: Source type (`"tile"`, `"route"`, or `"name"`, `const char *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_panel(Evas_Object *parent);` | Creates a collapsible/slide-out panel widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `void` | `void gfx_panel_set_orient(Evas_Object *object, Elm_Panel_Orient orient);` | Sets the orientation/slide direction of a panel. | \- `object`: Panel object (`Evas_Object *`). \- `orient`: `ELM_PANEL_ORIENT_TOP`, `ELM_PANEL_ORIENT_BOTTOM`, `ELM_PANEL_ORIENT_LEFT`, `ELM_PANEL_ORIENT_RIGHT`. |
| `Elm_Panel_Orient` | `Elm_Panel_Orient gfx_panel_get_orient(const Evas_Object *object);` | Returns the current orientation of a panel. | \- `object`: Panel object (`const Evas_Object *`). |
| `void` | `void gfx_panel_set_hidden(Evas_Object *object, Eina_Bool hidden);` | Hides or reveals the panel content. | \- `object`: Panel object (`Evas_Object *`). \- `hidden`: `EINA_TRUE` (hidden) or `EINA_FALSE` (visible). |
| `Eina_Bool` | `Eina_Bool gfx_panel_get_hidden(const Evas_Object *object);` | Returns whether the panel is hidden. | \- `object`: Panel object (`const Evas_Object *`). |
| `void` | `void gfx_panel_toggle(Evas_Object *object);` | Toggles the hidden/visible state of a panel. | \- `object`: Panel object (`Evas_Object *`). |
| `void` | `void gfx_panel_set_scrollable(Evas_Object *object, Eina_Bool scrollable);` | Enables or disables scrolling behavior on a panel. | \- `object`: Panel object (`Evas_Object *`). \- `scrollable`: `EINA_TRUE` or `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_panel_get_scrollable(const Evas_Object *object);` | Returns whether the panel is scrollable. | \- `object`: Panel object (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_panes(Evas_Object *parent);` | Creates a split-view panes container widget. | \- `parent`: Parent object (`Evas_Object *`). |
| `void` | `void gfx_panes_set_horizontal(Evas_Object *object, Eina_Bool horizontal);` | Sets whether the panes split horizontally or vertically. | \- `object`: Panes object (`Evas_Object *`). \- `horizontal`: `EINA_TRUE` (horizontal split) or `EINA_FALSE` (vertical split). |
| `Eina_Bool` | `Eina_Bool gfx_panes_get_horizontal(const Evas_Object *object);` | Returns whether the panes are split horizontally. | \- `object`: Panes object (`const Evas_Object *`). |
| `void` | `void gfx_panes_set_leftsize(Evas_Object *object, double size);` | Sets the relative size ratio (`0.0`–`1.0`) of the left/top pane. | \- `object`: Panes object (`Evas_Object *`). \- `size`: Size ratio (`double`, `0.0`–`1.0`). |
| `double` | `double gfx_panes_get_leftsize(const Evas_Object *object);` | Returns the relative size ratio of the left/top pane. | \- `object`: Panes object (`const Evas_Object *`). |
| `void` | `void gfx_panes_set_rightsize(Evas_Object *object, double size);` | Sets the relative size ratio (`0.0`–`1.0`) of the right/bottom pane. | \- `object`: Panes object (`Evas_Object *`). \- `size`: Size ratio (`double`, `0.0`–`1.0`). |
| `double` | `double gfx_panes_get_rightsize(const Evas_Object *object);` | Returns the relative size ratio of the right/bottom pane. | \- `object`: Panes object (`const Evas_Object *`). |
| `void` | `void gfx_panes_set_fixed(Evas_Object *object, Eina_Bool fixed);` | Locks or unlocks the divider position of the panes. | \- `object`: Panes object (`Evas_Object *`). \- `fixed`: `EINA_TRUE` (fixed) or `EINA_FALSE` (draggable). |
| `Eina_Bool` | `Eina_Bool gfx_panes_get_fixed(const Evas_Object *object);` | Returns whether the panes divider is fixed. | \- `object`: Panes object (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_photocam(Evas_Object *parent);` | Creates a photocam widget for viewing large images with zoom and pan. | \- `parent`: Parent object (`Evas_Object *`). |
| `Eina_Error` | `Eina_Error gfx_photocam_set_file(Evas_Object *object, const char *file);` | Loads an image file into a photocam widget. | \- `object`: Photocam object (`Evas_Object *`). \- `file`: Image file path (`const char *`). |
| `const char *` | `const char *gfx_photocam_get_file(const Evas_Object *object);` | Returns the image file path loaded in a photocam widget. | \- `object`: Photocam object (`const Evas_Object *`). |
| `void` | `void gfx_photocam_set_zoom(Evas_Object *object, double zoom);` | Sets the zoom factor of a photocam widget. | \- `object`: Photocam object (`Evas_Object *`). \- `zoom`: Zoom factor (`double`). |
| `double` | `double gfx_photocam_get_zoom(const Evas_Object *object);` | Returns the current zoom factor of a photocam widget. | \- `object`: Photocam object (`const Evas_Object *`). |
| `void` | `void gfx_photocam_get_imagesize(const Evas_Object *object, int *w, int *h);` | Reads the original pixel dimensions of the image loaded in photocam. | \- `object`: Photocam object (`const Evas_Object *`). \- `w`, `h`: Dimension output pointers (`int *`). |

&nbsp;