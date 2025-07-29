# zsfer's unity style guide

*disclaimer: these are just my own opinions and i will start using these conventions from now on!*

## General rules

- I like using [Guard Clauses](https://deviq.com/design-patterns/guard-clause).
- Must follow SOLID principles if applicable.
  - S = Single responsibility, scripts/behaviour should do one thing and one thing only!!
    - `PlayerMovement.cs` should handle player movement only, no shooting, no funny business`
- Use getters and setters for public variables to protect ur values!!
  - `public float Health { get; private set; }` so that only any reference to this property can modify the health value
- Generics are fun if used correctly
- Do not overcomplicate the gameplay code, keep it simple yet organized

## File structure (Feature based)

```
Root
	> Assets/
		> _Features/
			> FeatureName/
				> Art/	
					> 2D/3D/
					> Audio/
					> Shaders/	
				> Scripts/
				> Prefabs/
				> Data/      <- this contains ScriptableObject data
	> Builds/ 	         <- this will be part of the .gitignore, builds are big and should be indivdually sourced.
```

## File structure (Standard)

```
Root
  > Assets/
    > Editor/
    > Models/
    > Textures/
    > Shader/
    > Scripts/
  > Build/
  .../
```

## Assets

| File Type | Prefix | Suffix | Example | 
------------|--------|---------|-------|
| Behaviour Scripts |  | | PlayerController.cs |
| ScriptableObject scripts | | Data | WeaponData.cs |
| (3d) Static mesh (props) | SM_ | | SM_ExplodingBarrel.fbx |
| (2d) Sprite/spritesheets | SPR_ | | SPR_PlayerBob.png |
| Textures | TEX_ | | TEX_Barrel_D.png |
| Diffuse/Albedo | TEX_ | _D | TEX_Barrel_D.png |
| Normal map | TEX_ | _NOR | TEX_Barrel_NOR.png |
| Scenes | SCN_ | | SCN_Master |

## Scene Structure

this applies for both Multi-scene and Single-scene

```
@Game
@UI
@Debug
World
  Area1
_Gameplay
```

put `@` for all GameObjects that are containers for scripts (`@Game` can also be `@GameManager`)

put `_` for "folders"
`_Gameplay` contains all dynamic/playable/interactable objects

## Coding conventions

We follow [Godot coding conventions](https://docs.godotengine.org/en/stable/tutorials/scripting/c_sharp/c_sharp_style_guide.html)!!! Microsoft and Unity conventions can suck ass

`PascalCase` for public variables, `_camelCase` for private variables

```c#
public class PlayerMovement : MonoBehaviour
{
    public float Damage = 100;

    [SerializeField]
    private float _moveSpeed = 5;
    private Rigidbody2D _rb;
    private Animator _anim;
    private Vector2 _inputVec;

    void Start()
    {
        _rb = GetComponent<Rigidbody2D>();
        _anim = GetComponent<Animator>();
    }

    void Update()
    {
        _inputVec = (transform.right * Input.GetAxisRaw("Horizontal") + transform.up * Input.GetAxisRaw("Vertical")).normalized;

        _anim.SetFloat("x", _inputVec.x);
        _anim.SetFloat("y", _inputVec.y);
    }

    private void FixedUpdate()
    {
        _rb.velocity = _inputVec * _moveSpeed;
    }
}
```

### Abstraction

As much as possible, use abstraction to its maximum.

**Everything should be `private` until we dont need them to be**

```c#
// Only this specific script needs access to this Rigidbody variable
Rigidbody _rb;
```

Use the `[SerializeField]` header if you want to expose the variable in the inspector

```c#
// I only want this script to access this but I also want to edit it in the inspector
[SerializeField]
Rigidbody _rb;
```

**Use `public` WITH GETTERS AND SETTERS**

```c#
// Other scripts can access this but not modify it
public float MoveSpeed { get; private set; }

// If i want to expose it in the editor, i use the [field: SerializeField] header
[field: SerializeField]
public float MoveSpeed { get; private set; }
```

**FOLLOW THESE!!**
| thing | prefix or something | bad ❌ | good ✔️ |
|-----|--|--|--|
| bool | Is_ | bool Dead; | bool IsDead; |
| events | On_ | GameOverEvent GameOver; | GameOverEvent OnGameOver; |
| handlers | Handle_ | void OnGameOver() | void HandleOnGameOver() |

### Guard clauses

A simple check that exits the function/condition immediately. Do this instead of nesting a billion If and Else statements.
```c#

// Instead of doing this
void Update()
{
  if (!_isSlowed || !_isDead)
  {
    // gameplay code

    if (...)
    {
      // more gameplay code
    }
  }
}

// DO THIS!!
void Update()
{
  if (_isSlowed || _isDead)
    return;

  // gameplay code
  if (...)
  {
    // more gameplay code
  }
}

// But this is fine too

void Update()
{
	if (_isSlowed || _isDead)
	{
		// do something when slowed or dead
		return;	
	}

	// ...gameplay code
}

```

## Code comments

public functions should have a short descriptive summary so we dont get confused.

```c#
/// <summary>
/// Damages the entity
/// </summary>
/// <param name="damage">HP to reduce from entity health</damage>
public void Damage(int damage) 
```

for [non-descriptive/summary/complaint/rant comments](https://www.youtube.com/watch?v=QZ6rLbu4LQw), please author it so we know who's complaining :3
```c#
if (brain.GetBool("Feeding"))
{
  PickedUp = col.gameObject;
  // (jsa): why do this? why not. fuck you
  brain = PickedUp.GetComponent<MouseDeerBehaviour>().Brain; 
}

```

## Collaboration

### In-Engine

Actual game scenes will live inside the Scenes folder, but each developer/designer will have their own **sandbox** scene (ex. SCN_John_CombatTest)

### Code 

All of the game lives inside the `master` branch, but this comes with some rules:
*reasoning: i dont want to deal with merge conflicts anymore, i trust my team members to be smart enough when it comes to game programming*

1. DO NOT PUSH BROKEN COMMITS. if the current build in-editor does not work, do not push it to master.
2. All changes should be reviewed by the technical lead. If you're not sure about the change, you can ask for a review by branching off and submitting a pull request. 
3. Code reviews will be frequent and all developers must comply/fix any changes immediately to ensure code quality

```


## Git commit messages

We use [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/) for all of our commit messages.
```
feat(core): enemy spawn by waves now!
fix(player): faster movement speed
```

To **link and close GitHub issues**, we put it inside the commit message
```
fix #13(player): removed bunny hopping
```
