# zsfer's unity style guide

*disclaimer: these are just my own opinions and i will start using these conventions from now on!*

## General rules

- I like using [Guard Clauses](https://deviq.com/design-patterns/guard-clause).
- Must follow SOLID principles if applicable.
  - S = Single responsibility, scripts/behaviour should do one thing and one thing only!!
    - `PlayerMovement.cs` should handle player movement only, no shooting, no funny business`
- Use getters and setters for public variables to protect ur values!!
  - `public float Health { get; private set; }` so that only ur script can modify health value
- Generics are fun if used correctly
- Do not over abstract, keep it simple yet organized

## File structure

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
| ScriptableObject scripts | | _Data | WeaponData.cs |
| (3d) Static mesh (props) | SM_ | | SM_ExplodingBarrel.fbx |
| (2d) Sprite/spritesheets | SPR_ | | SPR_PlayerBob.png |
| Textures | TEX_ | | TEX_Barrel_D.png |
| Diffuse/Albedo | TEX_ | _D | TEX_Barrel_D.png |
| Normal map | TEX_ | _NOR | TEX_Barrel_NOR.png |

## Scene Structure

```
@Game
@UI
@Debug
World
  Area1
_Gameplay
```

put `@` for all GameObjects that are containers for scripts (`@Game` can also be `@GameManager`)
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

**FOLLOW THESE!!**
| thing | prefix or something | bad ❌ | good ✔️ |
|-----|--|--|--|
| bool | Is_ | bool Dead; | bool IsDead; |
| events | On_ | GameOverEvent GameOver; | GameOverEvent OnGameOver; |

## Code comments

public functions should have a short descriptive summary so we dont get confused.

```c#
/// <summary>
/// Damages the entity
/// </summary>
public void Damage(int damage) 
```

for [non-descriptive/summary/complaint/rant comments[(https://www.youtube.com/watch?v=QZ6rLbu4LQw)], please author it so we know who's complaining :3
```c#
if (brain.GetBool("Feeding"))
{
  PickedUp = col.gameObject;
  // (zsfer): why do this? why not. fuck you
  brain = PickedUp.GetComponent<MouseDeerBehaviour>().Brain; 
}

```

## Git commit messages

We use [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/) for all of our commit messages.
```
feat(Core): enemy spawn by waves now!
fix(Player): faster movement speed
```

