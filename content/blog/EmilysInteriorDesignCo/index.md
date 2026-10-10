---
title: "Emily's Interior Design Co."
featured: true
description: "A game I made for my future wife"
summary: "A game I made for my future wife"
tags: ["Cozy", "Interior Design", "Cutsey", "Relaxing"]
---
## Summary

| Role | Technical Game Designer / Systems Designer |
|---|---|
| Team | Solo |
| Engine | Godot |
| Repo | Github |
| Duration | 3 Months |

{{< youtube id="jsufifWGi7Y" autoplay=true loop=true mute=true controls=false >}}


<p style="text-align: center;">
  <a href="https://docs.google.com/document/d/1G5iFl_wfXiDdE87Bc75E1q9pIrwjVGwb-Qso2SX_f5M/edit?usp=sharing">Design Doc</a>
</p>

## About
*Emily’s Interior Design Co.* is an Animal Crossing–esque interior design game created specifically for my then-girlfriend, now wife, who is studying interior design. Developed over the course of three months, this is my longest and most optimized project to date. In addition to targeting minimal hardware like the Anbernic RG-40XXH, the game was built with development efficiency in mind as I was also working part-time and studying full-time, leveraging modular, reusable systems to speed iteration and maintain consistency. This approach allowed me to balance performance constraints with rapid feature development and polish.

### Target Hardware
{{< figure src=anbernic-rg40xxh.png alt="The Target Hardware">}}

| Console Name | Anbernic RG-40XXH |
|---|---| 
| CPU | H700 quad-core ARM Cortex A53, 1.5GHz frequncy |
| GPU | Dual-core G31 MP2 |
| RAM | 1GB |
| Storage | 64GB TF/MicroSD |
| System | Linux 64-bit |

I chose the Anbernic RG-40XXH as the target hardware because it offered an affordable, physical way to give my girlfriend a game she could play without needing a powerful PC. At around $70, it was a budget-friendly option that supported a wide range of retro games, making it a practical platform for the project.

However, its limited hardware capabilities presented a significant technical challenge. The device was designed primarily for retro gaming rather than modern, graphically demanding titles, so I needed to optimize the game to run smoothly within its performance constraints. This made performance optimization a key consideration throughout development.

## Room Decoration Logic

### Placement Logic
There are four modes when it comes to placement logic for the game
- floor placement
- wall placement
- table placeable objects placement
- rug placement. 

#### *Floor placement*

Floor placement uses ray-plane intersection algorithm with the ray originating at the player camera and extending indefinetly, only colliding with the ground plane and not the wall planes by modifying the collision masks of both the ray in the code and the plane in the level editor.

{{< figure src=ray-triangle-intersect.jpg alt="A ray triangle intersect" >}}

```gdscript
func placing_object():
	var query = create_ray()
	query.collision_mask = (1 if !selected_object.is_on_wall else 2)
	to_place_move_label.text = "To Place"
	
	var collision = camera.get_world_3d().direct_space_state.intersect_ray(query)
	if selected_object:
		if collision:
			selected_object.visible = true
			
			selected_object.transform.origin = collision.position
			can_place = selected_object.check_placement()
		else:
			selected_object.visible = false
```
<a id="check-placement"></a>
the object that is being moved then checks if there are other floor objects overlapping by using the Area3D nodes in Godot. It will also check if it might be a wall object and will not worry about collisions in those cases.

```gdscript
func check_placement() -> bool:
  if overlaps.is_empty():
	placement_green()
	return true
  else:
    placement_red()
	return false
```

Obviously this is very oversimplified for the sake of relevency and does not take into account the the different item types and their interactions with eachother but we will get into that later.

#### *Wall placement*

Wall placement is simple and elegant, walls are assigned to a different collision mask than the floor allowing so theres no overlap. 
```gdscript
query.collision_mask = (1 if !selected_object.is_on_wall else 2)
```
Then depending on the wall theres a little bit of linear algebra that modifies the basis of the object depending on the walls normal to make sure that the orientation of the object is correct and flush with the wall.

```gdscript
if selected_object.is_on_wall:
	var basis = Basis.IDENTITY
	basis.z = collision.normal
	basis.y = Vector3(0,1,0)
	basis.x = basis.y.cross(basis.z) 
	selected_object.basis = basis
```

{{< figure src=wall-basis-calculation.png alt="a visualizations of the calculation" caption="Y Vector (Orange): Always points straight up, Z Vector (Purple): Always points straight forward and directly corresponds to the walls normal vector, X Vector (Red): Points Right and is decided by the cross product of the Y and Z Vector">}}


It does the same check for overlaps with other wall objects as before described in the [check_placement()](#check-placement) function

#### *Table-placeable Objects*

*Table-placeable* objects act identically to floor placeable objects, except for one core detail. When they collide with a *Table* object (The table object acts the exact same way as the normal floor placable object except for this use case) a ray is casted from above the object and and uses the same ray intersect calculation to place the object on the top face of the collision area. 

```gdscript
#...Continuation of the check_placement() function
if object_type == 2:
	var ray_start = global_transform.origin + Vector3(0, 10, 0) #start above the object
	var ray_end = global_transform.origin + Vector3(0, -10, 0) #end below the object
	
	var space_state = get_world_3d().direct_space_state
	var query = PhysicsRayQueryParameters3D.new()
	query.from = ray_start
	query.to = ray_end
	query.collide_with_bodies = false
	query.collide_with_areas = true
	query.exclude = [area.get_rid()] #exclude the object itself if needed
	var result = space_state.intersect_ray(query)
	if result and "object_type" in result.collider.get_parent().get_parent() and result.collider.get_parent().get_parent().object_type == 1:
		position.y = result.position.y
		placement_green()
		return true
```

{{< figure src=table-placeable.png alt="raycast to table" caption="Raycast to Table">}}

#### *Rug Objects*

Rug objects are just floor objects that dont recognize any other object types but themselves so they can be placed on under objects and other objects can be placed on them.
```gdscript
#...Continuation of check_placement() function
#if placing a rug
if object_type == 3:
	placement_green()
	return true

[...]

#if placing an object on a rug
var non_rug_found := false
	for overlap in overlaps:
		var object = overlap.get_parent().get_parent()
		if "object_type" in object and object.object_type != 3:
			non_rug_found = true
			break
	if non_rug_found == false:
		placement_green()
		return true
```
### Wallpaper and Flooring Logic
The wallpaper and flooring was rather simple, all it was was a shader that would tile with the lenght of the wall it was on this could be variable (as we will show in the room creation section) it was important to make sure it always fit correctly. The flooring was done in the exact same way.

## Room Creation

During production, I balanced full-time coursework and part-time employment alongside my personal commitments. With 14 distinct rooms to create, each featuring different dimensions, entrances, and, in some cases, custom logic, I needed an efficient and scalable workflow. To address this, I focused on developing editor-facing tools that made room creation and customization quick and intuitive.

### Base Room and it's Parameters

The foundation of each room is a reusable room.tscn component consisting of six inward-facing planes. This approach takes advantage of Godot's backface culling to create an enclosed space that the player can still see into.

#### *Vertical and Horizontal Size*

The base room begins as a simple, small box. However, because each room varies in its horizontal and vertical dimensions, I needed a way to resize the geometry without manually adjusting each wall.

To solve this, I implemented horizontal and vertical size parameters that can be adjusted directly in the Godot Inspector. I also created an editor script that updates the room's geometry in real time, allowing me to preview its dimensions while designing a level. When the room loads at runtime, the same parameters are reapplied to ensure its geometry matches the dimensions configured in the editor.
{{< figure src="changing-size.gif" alt="Changing the room size paramters" >}}

```gdscript
## --- Setters for inspector/runtime ---
func set_horizontal_size(size: int) -> void:
	if horizontal_size != size:
		horizontal_size = size
		update_walls()

func set_vertical_size(size: int) -> void:
	if vertical_size != size:
		vertical_size = size
		update_walls()

# --- Wall updates ---
func update_walls() -> void:
	if not is_inside_tree():
		return
	
	# Floor + ceiling
	floor.scale.x = horizontal_size + 8
	floor.scale.z = vertical_size + 8
	ceiling.scale = floor.scale

	# Horizontal scaling
	left_wall.position.x = floor.scale.x
	right_wall.position.x = -floor.scale.x
	front_wall.scale.x = right_wall.position.x - left_wall.position.x
	back_wall.scale.x = right_wall.position.x - left_wall.position.x

	# Vertical scaling
	front_wall.position.z = floor.scale.z
	back_wall.position.z = -floor.scale.z
	left_wall.scale.x = front_wall.position.z - back_wall.position.z
	right_wall.scale.x = front_wall.position.z - back_wall.position.z

func _ready() -> void:
	update_walls()
``` 
#### *Navigating Betwen Rooms*

Another challenge was creating a reliable way for the player to navigate between rooms through doorways and hallways. Exploration of the house was non-linear, so I needed a way to make entrances to new rooms really quickly.

<u>**Doors**</u>

{{< figure src="Doorway-InGame.gif" alt="Moving between rooms in game" >}}

To handle doorways, I created a reusable door scene component that manages the transition between rooms. When the player enters a doorway, a transition shader plays before transporting them to the designated entry point in the destination room. This allows a room to support multiple entrances without requiring a separate room for each entry configuration.

Each door also includes a marker that defines the player's spawn location upon arrival. By exposing the destination scene and door as editable parameters, I could configure room connections directly in the Inspector rather than hardcoding each transition and have the GameSingleton or Game Instance hanle the transition. This made it easier to establish and modify connections between the game's 14 rooms.

{{< figure src="OfficeDoor.png" alt="Example of Editor Facing tool for door" >}}

```gdscript
extends Node3D

var standard_material: StandardMaterial3D
@export var marker: Marker3D
@export var next_scene: String
@export var next_door: String
@export var transition_screen : Node3D
		
func enter_portal():
	GameSingleton.door_name = next_door
	transition_screen.exit = true
	transition_screen.enter = false
	transition_screen.next_scene = next_scene
```

<u>**Hallways/Portals**</u>

{{< figure src="Portal-InGame.gif" alt="Moving between rooms in game" >}}

Beyond doors, I realized I also needed a way to navigate between rooms through open entrances. To accomplish this, I created a simple vertex shader that displaced part of the wall's geometry, creating an opening for the player to walk through.

```glsl
shader_type spatial;

render_mode blend_mix, depth_draw_opaque, cull_back, diffuse_burley, specular_schlick_ggx;

uniform vec3 center = vec3(0,0,0);
uniform float scale = 1;

// ... Additional material and texture parameters

void vertex() {
    // ... Vertex and triplanar texture setup

    if (length(center) > 0.001) {
        if (abs(VERTEX.x - center.x) * 1./scale < 0.2) {
            VERTEX.z -= 4.0;
        }
    }
}

// ... Triplanar texture and fragment shader functions
```

*The shader works by displacing vertices near a specified center point, effectively creating an opening in the wall.*

To sell the effect, I placed several white planes around the opening, each with increasing opacity, to simulate light bleeding through the wall. The shader and planes would only activate when a marker was assigned to the wall. This marker let me control both the location and width of the opening, allowing me to position entrances wherever the level required them.

```gdscript
func check_marker():
    var mat := material_override as ShaderMaterial
    if not mat:
        return

    if marker and is_instance_valid(marker) and marker != null:
        # Only update when the marker actually moves
        if old_probe_position != marker.position:
            old_probe_position = marker.position
            fade_away.visible = true

            if name == "Left Wall" or name == "Right Wall":
                fade_away.global_position.z = marker.global_position.z
                fade_away.scale.x = marker.scale.x
            else:
                fade_away.global_position.x = marker.global_position.x
                fade_away.scale.x = marker.scale.x

            print(marker.scale.x)
            mat.set_shader_parameter("center", to_local(marker.global_position))
            mat.set_shader_parameter("scale", marker.scale.x)
    else:
        # Revert to default state when no marker
        mat.set_shader_parameter("center", Vector3.ZERO)
        fade_away.visible = false
```

{{< figure src="Room-Portals-Variables.png" alt="Assigning the markers" >}}

{{< figure src="Portal-Location.gif" alt="Moving the portal to the desired location" >}}

The solution was simple, but computationally inexpensive and effective at communicating the idea of an open passage between rooms.

### Optimization for the Rooms
Due to the hardware limitations of my target platform, much of my optimization work focused on improving room performance, with dynamic lighting being the primary bottleneck.

To address this, I implemented Godot's LightmapGI to bake the room's lighting in advance, reducing the need to calculate lighting dynamically at runtime. I chose Godot's Compatibility renderer because it uses OpenGL, which was necessary to support the Anbernic RG40XXH.

The performance impact was substantial. Initially, the game ran at just 2–4 FPS on the target hardware. After implementing baked lighting and iterating on the results, I was able to achieve approximately 25 FPS.

To accommodate the game's variable room dimensions, I baked the lighting using the largest possible room configuration (10 × 10). This allowed me to resize rooms during development while retaining the baked lighting, rather than needing to bake the lighting again for every room configuration.

{{< figure src="LightmapGI.png" alt="Godot's LightmapGI setup used to bake room lighting" >}}

## Ongoing Documentation

This write-up is still in progress. I’ll be expanding this section soon with deeper technical breakdowns, system architecture decisions, and optimization details.