# zsfer's unity style guide

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
