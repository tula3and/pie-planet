## About codes of Pie Planet

1. [NPC Image size]
2. [Structure of conversation window]
3. [Small tips]
4. [Errors]

## NPC Image size

- Check Preserve Aspect in Image option
- Make a same size for every images in conversation window

## Structure of conversation window

- In TalkManager, each sentence is organized as `"<character name>;<sentence>;<character image index>"`
- Use TalkManager in GameManager with `.Split(';')[index]`
  ```csharp
  talkName.text = talkData.Split(';')[0];
  portraitImg.sprite = talkManager.GetPortrait(id, int.Parse(talkData.Split(';')[2]));
  ```
- Make choices by clicking the question mark
  - Use collider names for each conversation
      ```csharp
            else if (hit.collider.name == "su25")
            {
                Destroy(hit.collider);
                Talk(2500);
                talkPanel.SetActive(isAction);
                question25_4 = true;
                whereAmI = 2500;
                hit.collider.GetComponent<Renderer>().material.color = new Color(1, 1, 1, 0);
            }
      ```
  - After the codes above, the codes below execute because `question25_4` is `true`
      ```csharp
              else if (question25_4)
              {
                  Talk(whereAmI);
                  talkPanel.SetActive(isAction);
                  
                  //choices
                  if (talkIndex == 2 && typeEffect.onSelect)
                  {
                      question25_4 = false;
                      qu25cho1.SetActive(true);
                      qu25cho2.SetActive(true);
                  }
              }
      ```
  - Functions for two choices - use talkIndex
      ```csharp
        public void qu25fir()
        {
            talkIndex += 1;
            Talk(whereAmI);
            talkPanel.SetActive(isAction);
            qu25cho1.SetActive(false);
            qu25cho2.SetActive(false);
            question25_4 = true;
        }

        public void qu25sec()
        {
            talkIndex += 2;
            Talk(whereAmI);
            talkPanel.SetActive(isAction);
            qu25cho1.SetActive(false);
            qu25cho2.SetActive(false);
            question25_4 = true;
        }
      ```
- Use ObjData
  - Do not have to check collider names
  - `Destory` and change question marks to transparent for invisibility
  - Fix null error and use `second` to decide the flow
    ```csharp
        void Update()
        {
            if (Input.GetMouseButtonDown(0))
            {
                Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
                RaycastHit2D hit = Physics2D.GetRayIntersection(ray, Mathf.Infinity);
                ObjData objData;

                if (second)
                {
                    Talk(whereAmI);
                    talkPanel.SetActive(isAction);
                }

            if (hit.collider != null)
                {
                    //Teleport
                    if (hit.collider.name == "enter1")
                    {
                        player.Teleport(-16.52f, -18.210f);
                    }

                    else if (hit.collider.name == "out1")
                    {
                        player.Teleport(-16.52f, -1.829295f);
                    }

                    //Talk
                    else if (hit.collider.GetComponent<ObjData>() != null)
                    {
                        objData = hit.collider.GetComponent<ObjData>();

                        if (objData.isObject)
                        {
                            portraitImg.color = new Color(1, 1, 1, 0);
                            whereAmI = objData.id;
                            ObjExplain(whereAmI);
                            talkPanel.SetActive(isAction);
                            second = true;
                        }

                        else
                        {
                            Debug.Log(objData.id);
                            portraitImg.color = new Color(1, 1, 1, 1);
                            Destroy(hit.collider);
                            whereAmI = objData.id;
                            Talk(whereAmI);
                            talkPanel.SetActive(isAction);
                            hit.collider.GetComponent<Renderer>().material.color = new Color(1, 1, 1, 0);
                            second = true;
                        }
                    }
    ```

## Small tips

- Is GameObject on? `<game object name>.activeSelf`
- Mouseover events
  - Script for non-button
    ```csharp
    using System.Collections;
    using System.Collections.Generic;
    using System.Data.SqlTypes;
    using UnityEngine;

    public class OvertheMouse : MonoBehaviour
    {
        public GameObject thisObject;

        void OnMouseEnter()
        {
            thisObject.SetActive(true);
        }

        void OnMouseOver()
        {
            thisObject.SetActive(true);
        }

        void OnMouseExit()
        {
            thisObject.SetActive(false);
        }
    }
    ```
  - For buttons - add `Event Trigger` under Button option

## Errors

- `unsigned reference exception`
  - Check the connection inside Unity
  - There must be `None`
- If button click doesn't work
  - Make a new `Canvas`
  - Do not delete `EventSystem`
- If UI doesn't fit your screen
  - Inside `Scene`, `Canvas` → `Canvas Scaler` → `Change UI Scale Mode` to `Scale With Screen Size`
