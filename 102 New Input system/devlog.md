# 🎮 Player Setup Guide – Unity Input System + Movement + Mouse Look

Hey dev! Welcome to the magic notes of setting up your **player movement** and **mouse look** using Unity’s new Input System.

Grab a coffee ☕, buckle in — we’re making your player walk, run, and look around like a real human (finally 😅).

---

## 🛠️ Step 1: Install Input System Package

- Go to **Window → Package Manager**.
- Search for **Input System** and hit **Install**.
- Unity will ask if you want to use the new system. Say **Yes** (we’re done with the dinosaur days of the old Input Manager 🦕).
- Unity might restart — don’t panic, it’s just leveling up.

---

## 🎮 Step 2: Create Input Actions Asset

- In the **Project window**, right-click → **Create → Input Actions**.
- Name it: `PlayerControls.inputactions`
- Double-click the file — and boom 💥 you’re inside the Input Actions Editor!

---

## 🔧 Step 3: Configure Input Actions

### Create an Action Map:

- Click the `+` next to **Action Maps** → name it: `Player`  
(think of this like your player’s input folder).

---

### Add a "Move" Action:

- Inside the Player Action Map, click `+` → **Add Action** → name it: `Move`.
- Set **Action Type** to `Value`  
  👉 _Why?_ Because movement gives us values (like direction), not just "press/release" states.
- Set **Control Type** to `Vector2`  
  👉 _Why?_ Because we want to move on X and Y axes — forward/backward and left/right = 2D movement plane.

---

### Bind WASD:

- Click the `+` next to Move → **Add Binding** → choose **2D Vector Composite**
- Now assign:
  - **Up** → `W`
  - **Down** → `S`
  - **Left** → `A`
  - **Right** → `D`

(Yes, it's just like every game ever — if you’re binding arrow keys instead, you’re probably building Flappy Bird 😆)

---

### Assign Input Actions to the Project

- Save the asset.
- In the Input Actions window, click the gear icon ⚙️ → Enable **Generate C# Class**
- Set class name: `PlayerControls`
- File path: `Assets/Scripts/PlayerControls.cs`
- Hit **Save (Ctrl + S)**

🎉 You now have a magical script that’ll help you read input easily.

---

## 🧍‍♂️ Step 4: Create Player Movement Script

Now it's time to make your character move! Create a new script like `PlayerMovement.cs`.

Make sure it:
- Reads `Move` input
- Moves Rigidbody or CharacterController
- Locks cursor
- And later, handles running, sitting, or doing the moonwalk 🕺

Test it at this point — just to make sure your character isn't frozen in time 🥶

---

## 🖱️ Step 5: Make Your Player Look with the Mouse

Time to make your player actually **see** where they’re going.

### Add a Look Action:

- Open `PlayerControls.inputactions` again.
- In the Player Action Map → Add Action → name it `Look`
- Set **Action Type** to `Value`  
  👉 _Why?_ Because mouse movement gives value data (not just “click”)
- Set **Control Type** to `Vector2`  
  👉 _Why?_ Because we want horizontal + vertical movement from the mouse

### Bind It To:

- `<Mouse>/delta` → best for FPS-style mouse movement (relative movement)

Or if you're making something fancy like an RTS:
- `<Mouse>/position` → for absolute mouse position

---

### Update Input Script

- Regenerate C# class again (click the ⚙️ if needed).
- In your `PlayerMovement.cs` script:
  - Add a `Look` input handler using `.ReadValue<Vector2>()`
  - Rotate camera and player accordingly
  - Clamp the vertical look (so the player doesn't break their neck 🤕)

  "using UnityEngine;
using UnityEngine.InputSystem;

public class PlayerMovement : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float mouseSensitivity = 2f;
    public Transform cameraTransform;

    private Rigidbody rb;
    private PlayerControls playerControls;
    private Vector2 moveInput;
    private float xRotation = 0f;

    void Awake()
    {
        rb = GetComponent<Rigidbody>();
        playerControls = new PlayerControls();

        Cursor.lockState = CursorLockMode.Locked;
        Cursor.visible = false;
    }

    void OnEnable()
    {
        playerControls.Player.Enable();
        playerControls.Player.Move.performed += OnMove;
        playerControls.Player.Move.canceled += OnMove;
        // Removed Look.performed subscription
    }

    void OnDisable()
    {
        playerControls.Player.Disable();
        playerControls.Player.Move.performed -= OnMove;
        playerControls.Player.Move.canceled -= OnMove;
    }

    private void OnMove(InputAction.CallbackContext context)
    {
        moveInput = context.ReadValue<Vector2>();
    }

    void Update()
    {
        // Read mouse delta directly every frame (POLLING)
        Vector2 lookInput = playerControls.Player.Look.ReadValue<Vector2>();

        // Mouse Rotation
        float mouseX = lookInput.x * mouseSensitivity * Time.deltaTime;
        float mouseY = lookInput.y * mouseSensitivity * Time.deltaTime;

        xRotation -= mouseY;
        xRotation = Mathf.Clamp(xRotation, -90f, 90f);

        cameraTransform.localRotation = Quaternion.Euler(xRotation, 0f, 0f);
        transform.Rotate(Vector3.up * mouseX);
    }

    void FixedUpdate()
    {
        // Movement (physics-based)
        Vector2 normalizedMove = moveInput.normalized;
        Vector3 moveDir = transform.forward * normalizedMove.y + transform.right * normalizedMove.x;
        moveDir *= moveSpeed * Time.fixedDeltaTime;

        rb.MovePosition(rb.position + moveDir);
    }
}"

---

## 🎥 Step 6: Add Cinemachine Camera

Now to make your third-person dreams come true:

1. Install **Cinemachine** from Package Manager (if not already).
2. Add a **Cinemachine Virtual Camera** to your scene.
3. Drag your player’s **head or camera target** into the “Follow” and “Look At” fields.
4. Adjust distance, rotation, damping — until it feels *juuust right* 🎯
5. Assign this virtual camera’s transform into your `cameraTransform` field in the script inspector.

---

## 🧪 Step 7: Run & Test!

- Hit **Play**.
- Move around with **WASD**.
- Look around with your **mouse**.
- If it all works, take a screenshot, smile proudly 😎

---

### 📌 Optional Add-ons

- Add `Jump` and `Run` actions later.
- Hook animations through Animator + Parameters.
- Set up crouch, crawl, flashlight toggle, etc.

---

