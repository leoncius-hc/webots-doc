## Connector

Derived from `Device`.


```
Connector {
  SFString   type                "symmetric"
  SFBool     isLocked            FALSE
  SFBool     autoLock            FALSE
  SFBool     unilateralLock      TRUE
  SFBool     unilateralUnlock    TRUE
  SFFloat    distanceTolerance   0.01  # [0,inf)
  SFFloat    axisTolerance       0.2   # [0,pi)
  SFFloat    rotationTolerance   0.2   # [0,pi)
  SFInt32    numberOfRotations   4
  SFBool     snap                TRUE
  SFFloat    tensileStrength     -1
  SFFloat    shearStrength       -1
}
```

### Description

`Connector` nodes are used to simulate mechanical docking systems, or any other
type of device, that can dynamically create a physical link (or *connection*)
with another device of the same type.

`Connector` nodes can only connect to other `Connector` nodes. At any time, each
connection involves exactly two `Connector` nodes (peer to peer). The physical
connection between two `Connector` nodes can be created and destroyed at run
time by the robot's controller. The primary idea of `Connector` nodes is to
enable the dynamic reconfiguration of modular robots, but more generally,
`Connector` nodes can be used in any situation where robots need to be attached
to other robots.

`Connector` nodes were designed to simulate various types of docking hardware:

- Mechanical links held in place by a latch
- Gripping mechanisms
- Magnetic links between permanent magnets (or electromagnets)
- Pneumatic suction systems, etc.

Connectors can be classified into two types, independent of the actual hardware
system:

*Symmetric connectors*, where the two connecting faces are mechanically (and
electrically) equivalent. In such cases both connectors are active.

*Asymmetric connectors*, where the two connecting interfaces are mechanically
different. In asymmetric systems there is usually one active and one passive
connector.

The detection of the presence of a peer `Connector` is based on simple distance
and angle measurements, and therefore the `Connector` nodes are a
computationally inexpensive way of simulating docking mechanisms.

### Field Summary

- `model`: specifies the `Connector`'s model. Two `Connector` nodes can connect only if their model strings are identical.
- `type`: specifies the connector's type, this must be one of: "symmetric", "active", or "passive". A "symmetric" connector can only lock to (and unlock from) another "symmetric" connector. An "active" connector can only lock to (and unlock from) a "passive" connector. A "passive" connector cannot lock or unlock.
- `isLocked`: represents the locking state of the `Connector`. The locking state can be changed through the API functions `wb_connector_lock()` and `wb_connector_unlock()`. The *locking state* means the current state of the locking hardware, it does not indicates whether or not an actual physical link exists between two connectors. For example, according to the hardware type, `isLocked` can mean that a mechanical latch or a gripper is closed, that electro-magnets are activated, that permanent magnets were moved to an attraction state, or that a suction pump was activated, etc. But the actual physical link exists only if `wb_connector_lock()` was called when a compatible peer was present (or if the `Connector` was auto-locked).
- `autoLock`: specifies if auto-locking is enabled or disabled. Auto-locking allows a connector to automatically lock when a compatible peer becomes present. In order to successfully auto-lock, both the `autoLock` and the `isLocked` fields must be TRUE when the peer becomes present, this means that `wb_connector_lock()` must have been invoked earlier. The general idea of `autoLock` is to allow passive locking. Many spring mounted latching mechanisms or magnetic systems passively lock their peer.
- `unilateralLock`: indicate that locking one peer only is sufficient to create a physical link. This field must be set to FALSE for systems that require both sides to be in the locked state in order to create a physical link. For example, symmetric connectors using rotating magnets fall into this category, because both connectors must be simultaneously in a magnetic "attraction" state in order to create a link. Note that this field should always be TRUE for "active" `Connectors`, otherwise locking would be impossible for them.
- `unilateralUnlock`: indicates that unlocking one peer only is sufficient to break the physical link. This field must be set to FALSE for systems that require both sides to be in an unlocked state in order to break the physical link. For example, connectors often use bilateral latching mechanisms, and each side must release its own latch in order for the link to break. Note that this field should always be TRUE for "active" `Connectors`, otherwise unlocking would be impossible for them.
- `distanceTolerance`: the maximum distance [in meters] between two `Connectors` which still allows them to lock successfully. The distance is measured between the origins of the coordinate systems of the connectors.
- `axisTolerance`: the maximum angle [in radians] between the *z*-axes of two `Connectors` at which they may successfully lock. Two `Connector` nodes can lock when their *z*-axes are parallel (within tolerance), but pointed in opposite directions.
- `rotationTolerance`: the tolerated angle difference with respect to each of the allowed docking rotations (see ).
- `numberOfRotations`: specifies how many different docking rotations are allowed in a full 360 degree rotation around the `Connector`'s *z*-axis. For example, modular robots' connectors are often 1-, 2- or 4-way dockable depending on mechanical and electrical interfaces. As illustrated in , if `numberOfRotations` is 4 then there will be 4 different docking positions (one every 90 degrees). If you don't wish to check the rotational alignment criterion this field should be set to zero.


%figure "Example of rotational alignment (numberOfRotations=4 and rotationalTolerance=22.5 deg)"
![Example of rotational alignment (numberOfRotations=4 and rotationalTolerance=22.5 deg)](pdf/connector_alignment.pdf.png)
%end


- `snap`: when TRUE: the two connectors do automatically snap (align, adjust, etc.) when they become docked. The alignment is threefold: 1) the two bodies are rotated such that their z-axes become parallel (but pointed in opposite directions), 2) the two bodies are rotated such that their y-axes match one of the possible rotational docking position, 3) the two bodies are shifted towards each other such that the origin of their coordinate system match. Note that when the `numberOfRotations` field is 0, step 2 is omitted, and therefore the rotational alignment remains free. As a result of steps 1 and 3, the connector surfaces always become superimposed.
- `tensileStrength`: maximum tensile force [in Newtons] that the docking mechanism can withstand before it breaks. This can be used to simulate the rupture of the docking mechanism. The tensile force corresponds to a force that pulls the two connectors apart (in the negative *z*-axes direction). When the tensile force exceeds the tensile strength, the link breaks. Note that if both connectors are locked, the effective tensile strength corresponds to the sum of both connectors' `tensileStrength` fields. The default value -1 indicates an infinitely strong docking mechanism that does not break no matter how much force is applied.
- `shearStrength`: indicates the maximum shear force [in Newtons] that the docking mechanism can withstand before it breaks. This can be used to simulate the rupture of the docking mechanism. The `shearStrength` field specifies the ability of two connectors to withstand a force that would makes them slide against each other in opposite directions (in the *xy*-plane). Note that if both connectors are locked, the effective shear strength corresponds to the sum of both connectors' `shearStrength` fields. The default value -1 indicates an infinitely strong docking mechanism that does not break no matter how much force is applied.

### Connector Axis System

A `Connector`'s axis system is displayed by Webots when the corresponding robot
is selected or when *Display Axes* is checked in Webots *Preferences*. The
*z*-axis is drawn as a 5 cm blue line, the y-axis (a potential docking rotation)
is drawn as a 5 cm red line, and each additional potential docking rotation is
displayed as a 4 cm black line. The bounding objects and graphical objects of a
`Connector` should normally be designed such that the docking surface
corresponds exactly to *xy*-plane of the local coordinate system. Furthermore,
the `Connector`'s z-axis should be perpendicular to the docking surface and
point outward from the robot body. Finally, the bounding objects should allow
the superposition of the origin of the coordinate systems. If these design
criteria are not met, the `Connector` nodes will not work properly and may be
unable to connect.


%figure "Connector axis system"
![Connector axis system](png/connector_axes.png)
%end

### Connector Functions
