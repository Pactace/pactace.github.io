---
title: "Emily's Interior Design Co."
description: "A game I made for my future wife"
summary: "Going over some cool tools and things I used for this 3 month project"
tags: ["Cozy", "Interior Design", "Cutsey", "Relaxing"]
---
{{< video
    src=ExampleGameplay.mp4
    caption="Example Gameplay"
    loop=true
    muted=true
    autoplay=true
>}}

<p style="text-align: center;">
  <a href="https://docs.google.com/document/d/1G5iFl_wfXiDdE87Bc75E1q9pIrwjVGwb-Qso2SX_f5M/edit?usp=sharing">Design Doc</a>
</p>

## About
*Emily’s Interior Design Co.* is an Animal Crossing–esque interior design game created specifically for my then-girlfriend, now fiancée, who is studying interior design. Developed over the course of three months, this is my longest and most optimized project to date. In addition to targeting minimal hardware like the Anbernic RG-40XXH, the game was built with development efficiency in mind as I was also working part-time and studying full-time, leveraging modular, reusable systems to speed iteration and maintain consistency. This approach allowed me to balance performance constraints with rapid feature development and polish.
## Placement Logic
There are four modes when it comes to placement logic for the game, floor placement, wall placement, table placeable objects placement and rug placement. 

### *Floor placement*

Floor placement uses ray-plane intersection algorithm with the ray originating at the player camera and extending indefinetly, only colliding with the ground plane and not the wall planes by modifying the collision masks of both the ray in the code and the plane in the level editor.


{{< figure src=ray-triangle-intersect.jpg alt="A ray triangle intersect" >}}

```
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


```
func check_placement() -> bool:
  if overlaps.is_empty():
	placement_green()
	return true
  else:
    placement_red()
	return false
```

Obviously this is very oversimplified for the sake of relevency and does not take into account the the different item types and their interactions with eachother but we will get into that later.

### *Wall placement*

Wall placement is simple and elegant, walls are assigned to a different collision mask than the floor allowing so theres no overlap. 
```
query.collision_mask = (1 if !selected_object.is_on_wall else 2)
```
Then depending on the wall theres a little bit of linear algebra that modifies the basis of the object depending on the walls normal to make sure that the orientation of the object is correct and flush with the wall.

```
if selected_object.is_on_wall:
	var basis = Basis.IDENTITY
	basis.z = collision.normal
	basis.y = Vector3(0,1,0)
	basis.x = basis.y.cross(basis.z) 
	selected_object.basis = basis
```

{{< figure src=wall-basis-calculation.png alt="a visualizations of the calculation" caption="Y Vector (Orange): Always points straight up, Z Vector (Purple): Always points straight forward and directly corresponds to the walls normal vector, X Vector (Red): Points Right and is decided by the cross product of the Y and Z Vector">}}


It does the same check for overlaps with other wall objects as before described in the [check_placement()](#check-placement) function

### *Table-placeable Objects*

*Table-placeable* objects act identically to floor placeable objects, except for one core detail. When they collide with a *Table* object (The table object acts the exact same way as the normal floor placable object except for this use case) a ray is casted from above the object and and uses the same ray intersect calculation to place the object on the top face of the collision area. 

```
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

### *Rug Objects*

Rug objects are just floor objects that dont recognize any other object types but themselves so they can be placed on under objects and other objects can be placed on them.
```
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

## Ongoing Documentation

This write-up is still in progress. I’ll be expanding this section soon with deeper technical breakdowns, system architecture decisions, and optimization details.