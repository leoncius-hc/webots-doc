## CameraFocus


```
CameraFocus {
  SFFloat  focalDistance     0  # Distance to the object we are focusing on (m)
  SFFloat  focalLength       0  # Focal length of the lens (m)
  SFFloat  maxFocalDistance  0  # (m)
  SFFloat  minFocalDistance  0  # (m)
}
```

### Description

The `CameraFocus` node allows the user to define a controllable focus for a
`Camera` device. The `CameraFocus` node should be set in the `focus` field of a
`Camera` node. The focal distance can be adjusted from the controller program
using the `wb_camera_set_focal_distance()` function.

### Field Summary

- The `focalDistance` field defines the distance to the focusing plane (i.e. the object we want to focus on).
- The `focalLength` field defines the distance from the optical centre of the lens to the sensor. Bigger this value is, larger the sharp area is.
- The `maxFocalDistance` and the `minFocalDistance` fields define respectively the maximum and minimum values for the focal distance of the camera focus. Hence, they represent the minimum and maximum values that can be passed to the `wb_camera_set_focal_distance()` function and they can be retrieved using the functions `wb_camera_get_min_focal_distance()` and `wb_camera_get_max_focal_distance()`.
