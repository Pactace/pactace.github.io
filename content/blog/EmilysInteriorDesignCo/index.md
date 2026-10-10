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
*Emily’s Interior Design Co.* is an Animal Crossing–esque interior design game created specifically for my then-girlfriend, now wife, who is studying interior design. Developed over the course of three months, this is my most optimized project to date. In addition to targeting minimal hardware like the Anbernic RG-40XXH, the game was built with development efficiency in mind as I was also working part-time and studying full-time, leveraging modular, reusable systems to speed iteration and maintain consistency. This approach allowed me to balance performance constraints with rapid feature development and polish.

### Target Hardware
{{< figure src=anbernic-rg40xxh.png alt="The Anbernic RG-40XXH" >}}

| Console | Anbernic RG-40XXH |
|---|---|
| CPU | Allwinner H700, quad-core ARM Cortex-A53 @ 1.5GHz |
| GPU | Mali-G31 MP2 |
| RAM | 1GB |
| Display | 4" 640x480 (4:3) |
| Storage | 64GB microSD |
| System | 64-bit Linux |

I chose the RG-40XXH because it let me give my girlfriend at the time a physical, portable game she could play without a powerful PC. At around $70, it was affordable and already built for handheld play.

The tradeoff is that it's designed for retro games, not modern 3D. Performance became a core design constraint from day one, shaping everything from the renderer choice to how rooms are built. My goal was a stable 30 FPS at native resolution.

#### Preliminary Optimizations
My first decision was to build the game on Godot's Compatibility renderer. I chose this because it was OpenGL-based which is by far the most optimized for a retro console, without it the game would literally be unplayable. I also turned off anti-aliasing, which gave me a preformance boost of **5 FPS**. While there was the tradeoff of losing some of Godot's features as well as making the game slightly less graphically beautiful, I knew I could work around it to still deliver the experience I wanted.

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

```
func change_wallpaper(instance: ShaderMaterial, instance_path: String):
	back_wall.material_override = instance.duplicate()
	front_wall.material_override = instance.duplicate()
	right_wall.material_override = instance.duplicate()
	left_wall.material_override = instance.duplicate()
```

### Selecting Objects Logic
{{< figure src=SelectingObject.gif alt="Selecting an Object">}}

{{< figure src=SwitchingTabs.gif alt="Switching Tabs">}}

Each room has its own UI overlay, which controls which objects are available there. A bathroom can offer different furniture than a bedroom, and the player never scrolls through items that don't fit.

Inventories are stored as `Resource` files, one per object type (floor, wall, table, and so on). The exception is rugs, which share a single inventory across every room. Each inventory is a dictionary mapping an object's **thumbnail path** to its **scene path**. The UI loads only the lightweight thumbnails up front, and the full 3D object is spawned when the player selects it (see [Soft Object References](#optimizing-objects)).

{{< figure src="UIOverlayInventory.png" alt="Inventories per room" caption="Per-room inventories shown in the UI overlay" >}}

```gdscript
extends Resource
class_name SittingRoomWallInventory

# Key: thumbnail path | Value: scene path
@export var objects := {
	"res://Models/Final Models/Bedrooms/FtrSconceWall/Textures/FtrSconceWall_icon.png": "res://Models/Final Models/Bedrooms/FtrSconceWall/FtrSconceWall.tscn",
	"res://Models/Final Models/Tower/FtrArtStarryNight/Textures/mBody_Alb.png": "res://Models/Final Models/Tower/FtrArtStarryNight/FtrArtStarryNight.tscn",
	# ...
}
```

***Tabs Per Object Type***

The overlay is built around a `TabContainer` with one tab per category: floor, table, wall, wallpaper, and flooring. When a room loads, it hands each tab its matching inventory, and each tab populates itself.

```gdscript
func assign_inventory_scripts(floor_inventory, placeable_inventory, wall_inventory, wallpaper_inventory, flooring_inventory):
	floor_furniture_tab.assign_inventory(floor_inventory)
	placeable_furniture_tab.assign_inventory(placeable_inventory)
	wall_furniture_tab.assign_inventory(wall_inventory)
	wallpaper_tab.assign_inventory(wallpaper_inventory)
	flooring_tab.assign_inventory(flooring_inventory)
```

Because rooms only pass in the inventories they need, adding a new room means filling out a few resource files rather than writing new UI code.

***Wall Mode Camera***

The tab container also tells the camera when the player is on the wall tab, so the camera can shift to a better angle for placing wall objects.

```gdscript
func update_is_wall_state() -> void:
	var changed = is_wall
	is_wall = (get_current_tab_control().name == "Wall Objects")
	if changed != is_wall:
		camera.wall_update(is_wall)
		is_wall_change.emit(is_wall)
```

***Building the Grid***

Each tab loops through its inventory and creates a panel per object with the thumbnail as its icon. The scene path is stored as metadata on the panel, so the object itself isn't loaded until it's needed.

```gdscript
func load_objects(dict: Dictionary) -> void:
	# ... clear old children
	for key in dict.keys():
		var panel = Panel.new()
		# ... size and style the panel

		var icon = TextureRect.new()
		icon.texture = load(key)  # thumbnail only
		panel.add_child(icon)

		# Store the path, not a preloaded scene
		panel.set_meta("scene_path", dict[key])
		add_child(panel)
```

***Controller Navigation***

The target device has no mouse, so the entire overlay is navigated with the D-pad. The grid tracks a selected row and column, highlights the current item, and scrolls to keep it in view. A short input delay stops a held direction from skipping across the grid, and each move plays a UI sound for feedback.

```gdscript
func _unhandled_input(event: InputEvent) -> void:
	# ... guard clauses (visibility, input cooldown)
	if event.is_action_pressed("Down"):
		selected_row = min(row_count - 1, selected_row + 1)
		moved = true
	# ... Up, Left, Right

	if moved:
		selected_col = clamp(selected_col, 0, grid[selected_row].size() - 1)
		highlight_selection(grid[selected_row][selected_col])
		_update_scroll_position(row_count)
		# ... play sound, start nav cooldown
```

***Selecting an Object***

When the player presses Accept, the tab decides what to do based on its type. Wallpaper and flooring swap the room's material directly. Everything else is loaded from its stored path, handed to the placement system, and the game switches into edit mode so placement logic takes over.

```gdscript
func _handle_accept_action() -> void:
	var scene_path: String = selected_object.get_meta("scene_path", "")
	# ... wallpaper and flooring branches swap room materials

	var packed_scene: PackedScene = get_resource(scene_path)
	placement_system.assign_selected_object(packed_scene, is_wall, in_resource_cache)
	game_state.switch_to_edit_mode()
```

This system allowed me to make many different rooms with many different inventories, without the work of manually populating each grid container with the objects I wanted.

### Getting the Objects into the Game

Early in development, it became clear that I wouldn't have enough time to model all the decorative objects before the deadline, so I decided to use an asset pack from the internet. I hand-picked the objects I wanted to use and wrote a Python export script to convert them from OBJ to GLB, the format Godot works well with.

From there, I created an import script to automate the setup of each object, including collision, multiple skins, and other game-specific attributes. For example, wall objects required a specific wall-raycast object to function correctly in-game.

Initially, I imported the objects manually to understand what each model needed to work as intended. However, after calculating the time required to process hundreds of objects individually, I realized the task could take around six months. By developing the export and import scripts over approximately two weeks, with the help of AI when needed, I created a much faster workflow that allowed me to bring any of the selected objects into the game with minimal manual setup.

**Example of Fine Tuning Imports**
```gdscript
func change_collision_layers(scene_root_path: String):
	var scene := load(scene_root_path)
	var inst = scene.instantiate()
	var collision_node: StaticBody3D = null
	
	for i in range(inst.get_child_count()):
		var child = inst.get_child(i)
		if child.get_child_count() > 0:
			collision_node = child.get_child(0)
			break
	if collision_node:
		if not collision_node.get_collision_layer_value(1) and \
		collision_node.get_collision_layer_value(3) and \
		collision_node.get_collision_layer_value(8):
			print("⏩ Collision layers already set:", scene_root_path)
			return
		collision_node.set_collision_layer_value(1, false)
		collision_node.set_collision_layer_value(3, true)
		collision_node.set_collision_layer_value(8, true)
		collision_node.set_collision_mask_value(1, false)
		collision_node.set_collision_mask_value(3, true)
		collision_node.set_collision_mask_value(8, true)
	var packed := PackedScene.new()
	var ok := packed.pack(inst)
	if ok == OK:
		var err := ResourceSaver.save(packed, scene_root_path)
		if err == OK:
			print("✅ Final scene saved:")
		else:
			push_error("❌ Failed to save scene")
	else:
		push_error("❌ Failed to pack scene:")
```

### Optimizing Objects
To optimize the objects in the game I decided to do three things.
1. **Changing the Shading From Fragment to Vertex**:
for both the main character and the objects in the room I decided to force vertex shading in the project settings.
{{< figure src="ShadingSettings.png" alt="Forcing Vertex Shading" >}}
This gave me a performance boost of **~10 FPS** and allowed me to render a lot more objects without having to worry about shading overhead.
2. **Removing Shadows**:
For each object including the main character I removed shadows, this drastically improved performance by about **5 FPS** and was unnoticable on such a small screen. 
3. **Soft Object References**:
I had ton of objects that could be in a given room and loading them during runtime was so expensive. So instead of loading them at runtime I would just give the image a path and it would spawn the object and add it to a cache if the player wanted to add more objects of that type.

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

### Optimizing Rooms
Due to the hardware limitations of my target platform, much of my optimization work focused on improving room performance, with dynamic lighting being the primary bottleneck.

To address this, I implemented Godot's LightmapGI to bake the room's lighting in advance, reducing the need to calculate lighting dynamically at runtime. I chose Godot's Compatibility renderer because it uses OpenGL, which was necessary to support the Anbernic RG40XXH.

The performance impact was substantial. Initially, the game ran at just 2–4 FPS on the target hardware. After implementing baked lighting and iterating on the results, I was able to achieve approximately an increase of **20 FPS**.

To accommodate the game's variable room dimensions, I baked the lighting using the largest possible room configuration (10 × 10). This allowed me to resize rooms during development while retaining the baked lighting, rather than needing to bake the lighting again for every room configuration.

{{< figure src="LightmapGI.png" alt="Godot's LightmapGI setup used to bake room lighting" >}}