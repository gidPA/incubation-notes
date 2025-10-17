
1. Create a CameraManager instance
2. Start the CameraManager
3. Obtain a list of available cameras in the system.
4. Enumerate the camera list, and get the desired camera.
5. Acquire the selected camera so that the current program has an exclusive access to the camera.
6. Choose one or more camera roles. Available camera roles are as:
	1. a viewfinder,
	2. a still image capturer,
	3. a video recorder, or
	4. a raw image data capturer.
7. Generate a camera configuration and supply the selected camera roles.
8. Depending on the selected roles, there may be more than one stream configurations contained inside one CameraConfiguration object. Edit those so that the resulting captures match our expectations. The available stream configurations are:
	1. Pixel Format (Raw, MJPEG, RGB888, or many more)
	2. Size (width x height)
	3. Number of buffers.
	Stream configurations can be valid or invalid depending on the camera platform (CSI on raspberry pi, UVC on laptop webcams), so that it's best to query available configurations first.
9. Validate the CameraConfiguration. Result can either be:
	1. valid: The configuration is valid and needs no modification.
	2. adjusted: Some adjustment has to be made automatically to be valid. For instance, if one wants to adjust the resulting captures to have a resolution of 1920x1080 and the camera only provide  1280x720 at maximum, it will be clamped to 1280x720, and thus the result is "adjusted".
	3. invalid: The configuration contains invalid properties and cannot be automatically adjusted.
10. While not mandatory, it will be very useful to query available controls first, since the available controls will vary across camera hardwares and platforms.
11. Create a framebuffer allocator for the Camera, and allocate the framebuffers according to the available number of streams.
12. Create requests according to the number of buffers. While creating the requests, we can adjust the controls according to the available controls (if we query the available controls, that is...). 
	Note that if the control setting is not valid (for example, giving a string value to a control id that accepts enums, setting unsupported control id), either of this can happen:
	- An error log will appear (most often because of unsupported control id), or
	- An assertion failure will happen, and the program will crash (often caused by mismatching type)
13. Connect a callback function that will be executed when a request is completed. More about callback functions can be read [[Callback Function Cycle|here]].
14. Start the camera to begin capturing frames.
15. Queue the recently-made requests.
16. Capture will only work once for all created requests, unless we reuse the buffer and requeue the request inside the callback function. If we requeue the buffers, requests will run indefinitely until we stop it with signals or timers.
17. Stop the capture by stopping the camera.
18. Clean up by
	1. Disconnecting the request
	2. Releasing the camera,
	3. Stopping the camera manager, and
	4. Doing manual cleanup if needed (important if we use the C binding).