Выполнил: Смирнов Н.М
Группа: ЭВТ-70

Игровой движок: Unity 2021.3.15

Название работы: разработка игры Make Them Fall

Лабораторная работа 22

Тема: разработка игры Make Them Fall

Цель: создать игру Make Them Fall

Ход работы

1.Выполнение работы

Импортирование ресурсов в проект.

![image](https://user-images.githubusercontent.com/119733911/205502259-156e99a0-892d-468d-8971-458983aee469.png)

Рисунок 22.1 Используя редактор срезов получаем несколько спрайтов игрока.


Создаем объект Player и к нему объект Sprite, добавляем спрайт самого игрока 

![image](https://user-images.githubusercontent.com/119733911/205502288-8f6d2a35-38d9-4f94-b615-56dd6d9dbc66.png)

Рисунок 22.2 добавляем спрайт самого игрока 

![image](https://user-images.githubusercontent.com/119733911/205502293-183fddd8-6d1d-4ed0-a3a9-9848304a017d.png)

Рисунок 22.3 Размещение игрока и фона на сцене.

![image](https://user-images.githubusercontent.com/119733911/205502369-27da782a-ee81-4890-b1bb-40f2d0da6e2e.png)

Рисунок 22.4 Вид из окна Game

Листинг 22.1 Player.cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour
{
    public Vector2 xValue;
    public bool changeDirecionBool, leftFacing, changeIt, easing;

    Animator animator;
    Vector3 desiredPosition;

    void Start()
    {
        DesiredPosition();
        ChangeDirection();
        animator = GetComponent<Animator>();
    }   


    void Update()
    {
        if (changeIt)
        {
            if (easing)
            {
                transform.position = Vector3.Lerp(transform.position, desiredPosition, .1f);
            }
            else
            {
                transform.position = desiredPosition;
            }

            transform.position = Vector3.Lerp(transform.position, desiredPosition, .1f);

            if (Mathf.Abs(desiredPosition.x - transform.position.x) <= 0.1f)
            {
                Debug.Log("Changing gavity");
                changeIt = false;
            }
        }
        animator.SetBool("ChangeDirection", changeDirecionBool);


    }

    public void CallThisMethod()
    {
        DesiredPosition();
        ChangeDirection();

    }

    public void DesiredPosition()
    {
        desiredPosition = new Vector2(changeDirecionBool ? xValue.x : xValue.y, transform.position.y);
    }

    void ChangeDirection()
    {
        changeDirecionBool = !changeDirecionBool;
        // transform.position = Vector3.Lerp(transform.position, desiredPosition,.1f);
        if(changeDirecionBool != leftFacing)
        {
            changeIt = true;

            leftFacing = changeDirecionBool;
        }

        Debug.Log(transform.position.x);
    }

    public void OnTriggerEnter2D (Collider2D collision)
    {
        if (collision.tag == "Spike")
        {
            Debug.Log("dead Player");
            FindObjectOfType<GameManager>().RestartScene();
        }
    }
}


Листинг 22.2 Spike.cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Spike : MonoBehaviour
{
    public float moveSpeed;
    public Vector2 minMaxValue;
    Vector2 pos;

    void Start()
    {
        
    }

    void Update()
    {
        //Vector3.Up = new Vector3(0,1,0)
        transform.position += Vector3.up * moveSpeed * Time.deltaTime;

        if (transform.position.y > minMaxValue.x)
        {
            pos = transform.position;
            pos.x = transform.position.x;
            pos.y = minMaxValue.y;
            transform.position = pos;
            ShowOrHide();
        }
    }

    void ShowOrHide()
    {
        if (Random.value > 0.5f)
        {
            Debug.Log("Disabling");
            transform.GetChild(0).gameObject.SetActive(false);
            return;
        }

        transform.GetChild(0).gameObject.SetActive(true);

    }
}

Листинг 22.3 Collider.cs
using UnityEngine;

public class Collider : MonoBehaviour
{
    public Transform PlayerTransform;

    public void CallPlayerMethod()
    {
        Debug.Log(PlayerTransform.name);
        PlayerTransform.GetComponent<Player>().CallThisMethod();
    }

}

Листинг 22.4 ClickChecker.cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ClickChecker : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            Vector2 pos = Camera.main.ScreenToWorldPoint(Input.mousePosition);
            RaycastHit2D hit = Physics2D.Raycast(pos, Vector2.zero);
            if(hit)
            {
                if (hit.transform.name == "LeftSide")
                {
                    hit.collider.GetComponent<Collider>().CallPlayerMethod();
                }
                else if (hit.transform.name == "RightSide")
                {

                    hit.collider.GetComponent<Collider>().CallPlayerMethod();
                }
                else
                {
                    Debug.Log("Unkown hit from click Checker");
                    return;
                }
            }
        }
    }
}

2.Вывод:
В ходе проделанной работы, мы разработали игру Make Them Fall.
