# Godot3DRecoil
Steal My Code Save Your Time

add this to the bottom of any script and call recoil to start a recoil animation with tweens. 


```gdscript


@export var recoilSnappiness:float

# Member variable for the Tween instance
var _recoil_tween: Tween = null

# Recoil properties (adjust these values to tune the effect)
var RECOIL_PITCH_STRENGTH: float = -10.0    # Degrees of vertical (pitch) rotation for the kick
var RECOIL_YAW_STRENGTH: float = 1.0     # Degrees of horizontal (yaw) rotation for the kick
var RECOIL_KICK_UP_STRENGTH: float = 0.1  # Units of upward positional kick
var RECOIL_KICK_BACK_STRENGTH: float = -0.3 # Units of backward positional kick

var RECOIL_KICK_DURATION: float = 0.01   # Very short duration for the initial, sharp kick
var RECOIL_RECOVERY_DURATION: float = 0.25 # Time in seconds for the recoil to dissipate back to zero

func recoil(recoilDir: Vector3, verticalRecoil: float) -> void:
	if _recoil_tween:
		_recoil_tween.kill()

	_recoil_tween = create_tween()

	# 2. Calculate the target "peak" rotation and position for the recoil.
	#    These are determined by adding the recoil strength to the node's
	#    current rotation and position.
	var target_recoil_rotation = rotation_degrees
	target_recoil_rotation.x -= verticalRecoil * RECOIL_PITCH_STRENGTH
	target_recoil_rotation.y += recoilDir.x * RECOIL_YAW_STRENGTH

	#var target_recoil_position = position # Corrected: using 'position'
	var target_recoil_position = Vector3(0,0,0) # Corrected: using 'position'
	target_recoil_position.y += RECOIL_KICK_UP_STRENGTH
	target_recoil_position.z -= RECOIL_KICK_BACK_STRENGTH

	_recoil_tween.parallel()
	_recoil_tween.tween_property(self, "rotation_degrees", target_recoil_rotation, RECOIL_KICK_DURATION)\
		.set_ease(Tween.EASE_OUT).set_trans(Tween.TRANS_SINE)
	_recoil_tween.tween_property(self, "position", target_recoil_position, RECOIL_KICK_DURATION)\
		.set_ease(Tween.EASE_OUT).set_trans(Tween.TRANS_SINE)
		
	_recoil_tween.chain()
	_recoil_tween.tween_property(self, "rotation_degrees", Vector3.ZERO, RECOIL_RECOVERY_DURATION)\
		.set_ease(Tween.EASE_OUT).set_trans(Tween.TRANS_SINE)
	_recoil_tween.tween_property(self, "position", Vector3.ZERO, RECOIL_RECOVERY_DURATION)\
		.set_ease(Tween.EASE_OUT).set_trans(Tween.TRANS_SINE)

	_recoil_tween.tween_callback(_recoil_tween.kill)

```
