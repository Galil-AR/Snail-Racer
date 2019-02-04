# Snail-Racer

# PC Snail Code
extends KinematicBody2D

export (float) var max_speed
export (float) var rotation_speed
var velocity = Vector2()


func _physics_process(delta):
	control(delta)
	move_and_slide(velocity)  
	
func control(delta):
	var rot_dir = 0
	if Input.is_action_pressed('turn_right'):
		rot_dir += 1
	if Input.is_action_pressed('turn_left'):
		rot_dir -= 1
	rotation = rotation + rotation_speed * rot_dir * delta
	if drift == false:
		velocity = 0
	if Input.is_action_pressed('forward'):
		velocity = Vector2(max_speed, 2).rotated(rotation)
	if Input.is_action_pressed('back'):
		velocity = Vector2(-max_speed/2, 2).rotated(rotation)
	if Input.is_action_pressed('boost'):
		max_speed = 400
		velocity != Vector2()
	else:
		max_speed = 250

# NPC Snail code
extends KinematicBody2D

export (float) var speed
export (float) var sensitivity

onready var velocity = Vector2(0, speed)

func _ready():
	return
	
func getSpeedFromPoint(point):
	var dirVector = point - position
	return (dirVector.length() * sensitivity) / dirVector.dot(
	dirVector)

func _process(delta):
	if ($LeftRay.is_colliding()):
		rotate(getSpeedFromPoint($LeftRay.get_collision_point()) + 
		PI/200)
	if ($RightRay.is_colliding()):
		rotate(-getSpeedFromPoint($RightRay.get_collision_point()))
	move_and_slide(-velocity.rotated(rotation))
	return
	
# Track TilesSet Camera
extends Node2D

func _ready():
	set_camera_limits()
	
func set_camera_limits():
	var map_limits = $Floor.get_used_rect()
	var map_cellSize = $Floor.cell_size
	$SnailPlayer/Camera2D.limit_bottom = map_limits.end.y * map_cellSize.y 
	$SnailPlayer/Camera2D.limit_top = map_limits.position.y * map_cellSize.y
	$SnailPlayer/Camera2D.limit_right = map_limits.end.x * map_cellSize.x
	$SnailPlayer/Camera2D.limit_left = map_limits.position.x * map_cellSize.x

# Title Menu
extends Control

func _on_StartGameButton_pressed():
	get_tree().change_scene("res://circuits/Track.tscn")

func _on_QuitGameButton_pressed():
	get_tree().quit()
