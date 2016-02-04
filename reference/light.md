## Light


```
Light {
  SFFloat   ambientIntensity   0        # [0,1]
  SFColor   color              1 1 1    # [0,1]
  SFFloat   intensity          1        # [0,1]
  SFBool    on                 TRUE
  SFBool    castShadows        FALSE
}
```

Direct derived nodes: `PointLight`, `SpotLight`, `DirectionalLight`.

### Description

The `Light` node is abstract: only derived nodes can be instantiated. Lights
have two purposes in Webots: (1) the are used to graphically illuminate objects
and (2) they determine the quantity of light perceived by `LightSensor` nodes.
Except for `castShadows`, every field of a `Light` node affects the light
measurements made by `LightSensor` nodes.

### Field Summary

- The `intensity` field specifies the brightness of the direct emission from the light, and the `ambientIntensity` specifies the intensity of the ambient emission from the light. Light intensity usually ranges from 0.0 (no light emission) to 1.0 (full intensity). However, when used together with `LightSensors`, and if real physical quantities such as Watts or lumen (lm) are desired, larger values of `intensity` and `ambientIntensity` can also be used. The `color` field specifies the spectral color properties of both the direct and ambient light emission as an RGB value.
- The `on` boolean value allows the user to turn the light on (TRUE) or off (FALSE).
- The `castShadows` field allows the user to turn on (TRUE) or off (FALSE) the casting of shadows for this `Light`. When activated, sharp shadows are casted from and received by any renderable object except for the semi-transparent objects, and the `IndexedLineSet` primitive. An object can be semi-transparent either if its texture has an alpha channel, or if its `Material`.`transparency` field is not equal to 1. Shadows are additive (Several lights can cast shadows). The darkness of a shadow depends on how the occluded part is lighted (either by an ambient light component or by another light). Activating the shadows of just one `Light` can have a significant impact on the global rendering performance, particularly if the world contains either lots of objects or complex meshes. Some shadow issues can occurs in closed spaces.
