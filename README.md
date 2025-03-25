# Code Snippets

Snippets of code I written for some of my games (for my personal portfolio use)

## Table of Contents

- Not Suica Game Snippets (Unity)
- KINGQuest Game Snippets (Unity)
- Flappy Peo Game Snippets (Unity)

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

4. To Further appeal to the player of the fanbase. I drew and added the Beloved Mascot of the Vtuber, Popomaru, as a moving Live2D model in the game following the player's cursor. Using the official Live2D SDK for Unity.  

```csharp


```
