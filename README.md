# Code Snippets

Snippets of code I written for some of my games (for my personal portfolio use)

## Table of Contents

- [Not Suica Game Snippets (Unity)](#totally-not-suica-game-snippets-unity)
- [KINGQuest Game Snippets (Unity)](#kingquest-game-snippets-unity)

## Totally Not Suica Game Snippets (Unity)

#### As of this project I'm in charge of the mechanics and the overall art of the game. I'm also in charge of the game's UI/UX design. The game is a 2D Sandbox Puzzle Fan Game based on the hit game "Suica Game" as a tribute to the Vtuber, Yukinoshita Peo.

Here are some of the code snippets I wrote for the game:

1. This is the logic for updating the leaderboard on GameOver

```csharp
// The code is placed in the GameManager Singleton class

    public void GameOver()
    {
        if (state == GameState.OutOfGame) return;
        popomaru.SetActive(false);

        state = GameState.OutOfGame;

        for (int i = 0; i < highScores.Count; i++)
        //loop through to check if there is a podium top score, and if so, add it to the list, sort by descending, then remove the last place so it's back to a total of 3
        {
            if(score > highScores[0])
            {
                isSubmitable = true;
            }
            if (score > highScores[i])
            {
                highScores.Add(score);

                highScores.Sort();

                highScores.Reverse();

                highScores.RemoveAt(highScores.Count - 1);


                break;
            }
        }

        for (int i = 0; i < highScores.Count; i++)
        {
            PlayerPrefs.SetInt("hs" + i.ToString(), highScores[i]);
        }

        player.EndGame();

        StoreManager.instance.AddMoney(score);


        UIManager.instance.ShowGameOverMenu();

        UIManager.instance.GameOverScore.text = score.ToString();

        /*Due to Time Constraints we had to remove the gacha feature from the game*/
        // GachaManager.instance.DispenseBall();

    }



```

2. One of the Flagship Mechanics of the the game is the theme being a Gachapon Machine. So I implemented GameOver animations and a Randomizer for the Gacha Balls prefab to make them more appealing to the player.

```csharp
// This is the script for the GameOver Sequence Animation
using System.Collections;
using System.Collections.Generic;
using System.Security.Cryptography;
using UnityEngine;
using UnityEngine.SocialPlatforms.Impl;

public class GameOverSequence : MonoBehaviour
{
    // Start is called before the first frame update
    [SerializeField] private GameObject portalIn;
    [SerializeField] private GameObject portalOut;


    private void Awake()
    {
            portalIn = GameObject.FindGameObjectWithTag("PortalIn");
            portalOut = GameObject.FindGameObjectWithTag("PortalOut");
    }

    private void OnCollisionEnter2D(Collision2D other)
    {
        if (other.gameObject.CompareTag("Fruits")) {

            if(other.gameObject.transform.childCount > 0)
            {
                for (int i = 0; i < other.gameObject.transform.childCount; i++)
                {
                    // Collect all the items in the boundary
                    if(other.gameObject.transform.GetChild(i).CompareTag("Items"))
                    {
                        other.gameObject.transform.GetChild(i).GetComponent<Rigidbody2D>().simulated = false;
                        other.gameObject.transform.GetChild(i).GetComponent<Collider2D>().enabled = false;
                    }
                }
            }
            // Collision manipulation and Velocity Randomizer for "Pop-Out" Animation
            Physics2D.IgnoreCollision(other.gameObject.GetComponent<Collider2D>(), GetComponent<Collider2D>());
            other.gameObject.GetComponent<Rigidbody2D>().angularVelocity = Random.Range(-30,30);
            other.gameObject.GetComponent<Rigidbody2D>().velocity = new Vector2(Random.Range(-5,5),Random.Range(-5,5));
            other.gameObject.GetComponent<Rigidbody2D>().mass = Random.Range(0.1f,0.5f);
            other.transform.position = portalOut.transform.position;
            if(other.gameObject.TryGetComponent<SpriteRenderer>(out SpriteRenderer parentRenderer))
            {
               parentRenderer.sortingLayerName = "PreGameOver";
            }
            else if(other.gameObject.transform.childCount > 0)
            {
                for (int i = 0; i < other.gameObject.transform.childCount; i++)
                {
                    if(other.gameObject.transform.GetChild(i).TryGetComponent<SpriteRenderer>(out SpriteRenderer spriteRenderer))
                    {
                        spriteRenderer.sortingLayerName = "PreGameOver";
                    }
                }
            }

            StartCoroutine(Wait(other.gameObject));
        }
    }


    private IEnumerator Wait(GameObject other) {/*Wait Logic Omitted*/}
}
```

```csharp
// This is the snippet for the Gacha Ball Sprite Randomizer
this.GetComponent<SpriteRenderer>().sprite = sprites[Random.Range(0, sprites.Length)];

// This is the snippet for the Gacha Ball Enmote (GIF) Randomizer
public GameObject[] emotes; //Array of emote GIFs for eyedropping

void Awake() // To call before Start()
    {
        emotes[Random.Range(0, emotes.Length)].SetActive(true);
    }

```

3. As the game can be played on the web on both Mobile and PC, I implemented a simple touch control system and On-Screen Keyboard for the game.

```csharp
// Snippet for Onscreen Keyboard Input
public void EnableKeyboard() {
    if (Application.platform == RuntimePlatform.WebGLPlayer && Application.isMobilePlatform)
    {
        keyboard.SetActive(true);
        textBox.text = "";
    }
}
```

4. To Further appeal to the player of the fanbase. I drew and added the Beloved Mascot of the Vtuber, Popomaru, as a moving Live2D model in the game following the player's cursor. Using the official Live2D SDK for Unity. (Since the game uses a Legacy Version of Live2D SDK, the code needs to be reimplemented in the future)

```csharp
// As Live2D Keyframes value can't be updated immediately in the Update() function, LateUpdate() is used instead
void LateUpdate()
{
    if(lerpIn)
    {
        StartCoroutine(Lerp(startVal:0.0f,endVal:1.0f,lerpDuration:1f,_lerpIn:true));
    }
    else if(lerpOut)
    {
        StartCoroutine(Lerp(startVal:1.0f,endVal:0.0f,lerpDuration:1f,_lerpIn:false));
    }
}

//Petting Animation Logic using Mouse Drag
public void OnMouseDrag()
{
    if(inPatRange)
    {
        // overshoot = true;
        var _animator = GetComponentInParent<Animator>();
        _animator.SetBool("startPatting",true);
    }
}
```

---

## KINGQuest Game Snippets (Unity)

#### For this project, it is submitted to the Fifth HoloJam. I was the project lead and head developer. The game is a 2D Platformer Game based on the game, "Megaman X". The game is a direct reference to Vtuber, Shirakami Fubuki's Song: ["KINGWORLD"](https://youtu.be/yVaQpUUAzik?si=B7GkiBKiRwycXfqJ).

_The project had a number of unfixed bugs due to Game Jam time constraints._

Here are some of the code snippets I wrote for the game:

1. Using a Self-Customized version of [ta-davis-yu's 2D Platformer Hunter Engine](https://github.com/ta-david-yu/2D-Platformer-Hunter) allows the player to have better raycasting, better movement and attacking mechanics.

   ##### Enemy and Player Knockback Mechanics (w/ Cooldown to prevent stunlocking)

   ```csharp
   public IEnumerator FakeAddForceMotion(GameObject target, EnemyData enemyData, float facingDirection)
   {
       float i = 0.01f;
       Rigidbody2D rb = target.GetComponent<Rigidbody2D>();
       Vector2 raycastDirection = new Vector2(-facingDirection, 0);
       float raycastDistance = .7f;
       float topRaycastDistance = .2f;
       int worldLayerMask = LayerMask.GetMask("World");

       int numberOfRaycasts = 20;
       float padding = 0.1f;
       Collider2D collider = target.GetComponent<Collider2D>();
       float colliderHeight = collider.bounds.size.y;
       float raycastSpacing = (colliderHeight) / (numberOfRaycasts - 1);
       while (enemyData.knockbackForce >= i)
       {
           bool obstacleDetected = false;

           for (int j = 0; j < numberOfRaycasts; j++)
           {
               // Adjust the raycast origin for each raycast line
               Vector2 raycastOrigin = new Vector2(target.transform.position.x, collider.bounds.min.y + padding + j * raycastSpacing);
               RaycastHit2D hit = Physics2D.Raycast(raycastOrigin, raycastDirection, raycastDistance, worldLayerMask);

               // Draw the raycast line (DEBUGGING)
               Debug.DrawLine(raycastOrigin, raycastOrigin + raycastDirection * raycastDistance, Color.red);

               if (hit.collider != null)
               {
                   // Obstacle detected, stop the player's velocity
                   Debug.Log("Obstacle detected");
                   rb.linearVelocity = Vector2.zero;
                   obstacleDetected = true;
                   break;
               }
           }

           // Add a raycast on top of the collider
           Vector2 topRaycastOrigin = new Vector2(target.transform.position.x, collider.bounds.max.y - padding);
           RaycastHit2D topHit = Physics2D.Raycast(topRaycastOrigin, raycastDirection, topRaycastDistance, worldLayerMask);

           // Draw the top raycast line (DEBUGGING)
           Debug.DrawLine(topRaycastOrigin, topRaycastOrigin + raycastDirection * topRaycastDistance, Color.blue);

           if (topHit.collider != null)
           {
               // Obstacle detected, stop the player's velocity
               Debug.Log("Obstacle detected on top");
               rb.linearVelocity = Vector2.zero;
               obstacleDetected = true;
           }

           if (obstacleDetected)
           {
               yield break;
           }

           // Apply the knockback force
           rb.linearVelocity = new Vector2(enemyData.knockbackForce / i * -facingDirection, 0.1f); // For X axis positive force
           i += Time.deltaTime;
           yield return new WaitForEndOfFrame();
       }

       rb.linearVelocity = Vector2.zero;
       yield return null;
   }

   ```

   ##### Mana Mechanics for the Player

   ```csharp
   private void CheckMana()
   {
       playerMana += (Stats.ManaRecoverPerSec + currentBuff.RecoverMana * Stats.BuffManaRecover) * Time.deltaTime;

       if (playerMana > maxMana)
       {
           playerMana = maxMana;
       }

       EventManaUpdate(playerMana, maxMana);
   }
   public bool CanDash()
   {
       return playerMana >= Stats.DashManaCost;
   }
   ```

   ##### Invincible Frames for Dashing (Allows for the player to be invincible for a short period of time after dashing)

   ```csharp
   public void OnDash(int direction)
   {
       StopCoroutine(DashIFramesPadding());
       // Change the player's layer to the dash layer
       gameObject.layer = LayerMask.NameToLayer("DashLayer");

       instance.isInvincible = true;

       playerMana -= Stats.DashManaCost;
       EventManaUpdate(playerMana, maxMana);

       PlaySound(dashSound);
       // Revert the player's layer back to the original layer
   }
   private void OnDashEnd()
   {
       StopCoroutine(DashIFramesPadding());
       StartCoroutine(DashIFramesPadding());
   }
   ```

   ##### Crude Weapon Switching and Attack Mechanics (Sword & Gun)

   ```csharp
   private Animator _animator;

   void Awake()
   {
       _animator = GetComponent<Animator>();
   }

   void Update()
   {
       HandleWeaponSwitchInput();
       HandleFireInput();
       UpdateEmission();
       CheckMana();
   }

   private void HandleWeaponSwitchInput()
   {
       if (Input.GetKeyUp(KeyCode.Alpha1) || Input.GetKeyUp(KeyCode.Alpha2))
           isSwitchingWeapons = false;

       if (isSwitchingWeapons) return;

       if (Input.GetKeyDown(KeyCode.Alpha1) && !isFiring)
           SwitchWeapon(1, false, 0);
       else if (Input.GetKeyDown(KeyCode.Alpha2) && !isSlashing)
           SwitchWeapon(2, true, 1);
   }

   private void SwitchWeapon(int weaponId, bool shootReady, int actionState)
   {
       isSwitchingWeapons = true;
       currentWeapon = weaponId;
       _animator.SetBool("ShootReady", shootReady);
       _animator.SetInteger("ActionState", actionState);
   }

   private void HandleFireInput()
   {
       if (Input.GetButton("Fire1"))
       {
           if (currentWeapon == 1)
           {
               _animator.SetTrigger("Slash");
               isSlashing = true;
               _animator.SetBool("isSlashing", true);
           }
           else if (currentWeapon == 2 && Time.time >= nextFireTime)
           {
               Shoot();
               isFiring = true;
               _animator.SetTrigger("Fire");
               nextFireTime = Time.time + fireCooldown;
           }
       }
       else
       {
           if (currentWeapon == 1)
           {
               isSlashing = false;
               _animator.SetBool("isSlashing", false);
           }
           else
               isFiring = false;
       }
   }

    // Parent "Trail Emission" with grounded player
   private void UpdateEmission()
   {
       emission.enabled = _animator.GetBool("isGrounded");
   }
   ```

2. The game has a World Corruption effect that shows the gradual corruption of the world. The effect is a simple shader and Particle System that uses a noise texture to create a distortion effect. The shader is applied to the camera using a custom script.

3. After Images of the player occurs when the player dashes. This effect is achieved by the AfterImage GeneratorScript

````csharp
public IEnumerator GenerateAfterImage()
    {
        GameObject _afterImage = Instantiate(afterImage, player.transform.position, player.transform.rotation);
        _afterImage.transform.localScale = player.transform.localScale;
        _afterImage.GetComponent<SpriteRenderer>().enabled = true;
        _afterImage.GetComponent<SpriteRenderer>().sprite = player.GetComponent<SpriteRenderer>().sprite;
        yield return new WaitForSeconds(afterImageDelay);

    }
    ```
````
