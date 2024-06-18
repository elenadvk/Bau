Проектная работа «Bau»

1.	Правила игры
На поле имеется несколько объектов – кубы, шары, капсула и монеты.
Капсула – главный персонаж, которым управляет пользователь с помощью кнопок «W», «A», «D» и «S». Его цель – собирать монеты и, при необходимости, уничтожать врагов. Взаимодействовать с красными шарами игрок может с помощью своего оружия – белого йо-йо, которое появляется при нажатии на кнопку «L».
Кубы – это барьеры, наткнувшись на которые игрок разрушается. 
Шары – враги, которые в процессе игры нападают на игрока, если тот входит в их поле действия, пытаясь столкнуть его с игрового поля; если им это удается – игрок проигрывает. При уничтожении врагов главным игроком – возникают новые.
Монеты – элементы, которые собирает игрок в процессе игры. 
Уровень прохождения игры определяется количеством собранных монет и игровым временем (т.е. до проигрыша).
 
2.	Сцены
В игре реализовано две сцены – сцена меню и сцена игры.
Сцена MenuScene отвечает за меню, которое видит пользователь перед началом игры.
Сцена 7 – игровая сцена.
	Кнопка «Начать игру» переводит пользователя к сцене с игрой.
Кнопка «Настройки» переводит пользователя к окну.
Кнопка «Выход» осуществляет выход из игры и остановку фоновой музыки.

3.	Элементы игры
Из названий элементов можно определить, какой элемент относится к какому объекту.
 
4.	Материалы
В проекте каждый объект имеет свой материал. Используются следующие материалы: материал монеты, материал куба, материал поля, материал главного персонажа и материал врагов. 
 
Рисунок 7 – Материалы проекта
 
5.	Скрипты
Для каждого активного объекта есть свой скрипт. Все скрипты проекта представлены на следующих листингах.
ButtonScript отвечает за кнопки в меню (листинг 1).
Листинг 1 – ButtonScript.css
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class ButtonScript : MonoBehaviour
{
    public GameObject gameObjectPanel;
    public GameObject gameObjectImage;
    public Sprite Newsprite;

    public void Close()
    {
        gameObjectPanel.SetActive(false);
    }

    public void ChangeImage()
    {
        gameObjectImage.GetComponent<Image>().sprite = Newsprite;
    }
}
	Coin – скрипт для генерации монет (листинг 2).
Листинг 2 – Coin.css
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Coin : MonoBehaviour
{
    // Start is called before the first frame update
    public GameObject prefab;
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }

    void OnTriggerEnter(Collider other)
    {
        if(other.CompareTag("Player"))
        {
            Instantiate(gameObject, transform.position + new Vector3(Random.Range(-5, 5), 0, Random.Range(-5, 5)), transform.rotation);
            Destroy(gameObject);
        }
    }
}
	Скрипт DragnDrop обеспечивает перемещение объектов (листинг 3).
Листинг 3 – DragnDrop.css
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UIElements;

public class DragnDrop : MonoBehaviour
{
    private Plane dragPlane;
    private Vector3 offset;

    private void OnMouseDown()
    {
        dragPlane = new Plane(Camera.main.transform.forward, transform.position);
        Ray camRay = Camera.main.ScreenPointToRay(Input.mousePosition);

        float planeDist;
        dragPlane.Raycast(camRay, out planeDist);
        offset = transform.position - camRay.GetPoint(planeDist);
    }

    private void OnMouseDrag()
    {
        Ray camRay = Camera.main.ScreenPointToRay(Input.mousePosition);
        float planeDist;
        dragPlane.Raycast(camRay, out planeDist);
        transform.position = camRay.GetPoint(planeDist) + offset;

    }
}
	Скрипт enemy задает инструкции действия врагов (листинг 4).
Листинг 4 – enemy.css
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class enemy : MonoBehaviour
{
    float speed = 3;
    float attackDistance = 10;

    Transform target;
    
    void Start()
    {
        target = GameObject.FindGameObjectWithTag("Player").GetComponent<Transform>();
    }


    void FixedUpdate()
    {
        if (Vector3.Distance(transform.position, target.position) < attackDistance)
        {
            transform.position = Vector3.MoveTowards(transform.position, target.position, speed * Time.fixedDeltaTime);
        }
        
    }
    void OnTriggerEnter(Collider destroy_player)
    {
        if (destroy_player.gameObject.CompareTag("Destroy"))
        {
            Destroy(gameObject);

            //генерация новых
            Instantiate(gameObject, transform.position + new Vector3(Random.Range(-5, 5), 0, Random.Range(-5, 5)), transform.rotation);

        }
    }
}
	Скрипт MenuScript описывает действия, которые совершаются при нажатии на кнопки меню (листинг 5).
Листинг 5 – MenuScript.css
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class MenuScript : MonoBehaviour
{
    public void Quit()
    {
        UnityEditor.EditorApplication.isPlaying = false;
    } 

    public void startScene()
    {
        SceneManager.LoadScene("7");
    }
}
	Скрипт PlayerMovement описывает действия главного персонажа (листинг 6).
Листинг 6 - PlayerMovement.css
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    public Transform Cube;
    public Transform Plane2;
    public float speed;
    public int k;
    public Camera[] cameras;
    private int currentCamera=0;

    // public Vector3 direction;  

    private CharacterController characterController;
    void Start()
    {
        characterController = GetComponent<CharacterController>();
        k = 0;
    }

    void Update()
    {
        if(Input.GetKeyUp(KeyCode.E))
        {
            cameras[currentCamera].tag = "Untagged";
            cameras[currentCamera].enabled = false;
            currentCamera = (currentCamera + 1)%2;
            cameras[currentCamera].tag = "MainCamera";
            cameras[currentCamera].enabled = true;
        }
        if (Input.GetKeyUp(KeyCode.A))
        {
            GetComponent<Rigidbody>().velocity = new Vector3(0,0,speed);
        }
        if (Input.GetKeyUp(KeyCode.W))
        {
            // transform.Translate(direction); так падает
            GetComponent<Rigidbody>().velocity = new Vector3(speed,0,0); 
        }
        if (Input.GetKeyUp(KeyCode.S))
        {
            GetComponent<Rigidbody>().velocity = new Vector3(-speed,0,0);
        }
        if (Input.GetKeyUp(KeyCode.D))
        {
            GetComponent<Rigidbody>().velocity = new Vector3(0,0,-speed);
        }
    }
    void OnTriggerEnter(Collider destroy_player)
    {
        if (destroy_player.gameObject.CompareTag("Destroy"))
        {
            Destroy(gameObject);
        }
    }

}
	Скрипт SoundManager отвечает за регулирование звука в игре (листинг 7).

Листинг 7 – SoundManager.css
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SoundManager : MonoBehaviour
{
    private static SoundManager instance = null;
    // public AudioSource musicSource;

    void Awake()
    {
        if (instance != this && instance != null)
        {
            Destroy(this.gameObject);
            return;
        }
        instance = this;
        DontDestroyOnLoad(this.gameObject);
    }

    void Start()
    {
        GetComponent<AudioSource>().Play();
    }
}
	Скрипт SphereAnimation отвечает за анимацию оружия главного персонажа (листинг 8).
Листинг 8 – SphereAnimation.css
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SphereAnimationScript : MonoBehaviour
{
    private Animator characterAnimator;
    public AudioSource tickSource;

    void Start()
    {
        tickSource = GetComponent<AudioSource>();
    }
    
    private void Awake()
    {
        characterAnimator = GetComponent<Animator>();
    }
    private void Update()
    {
        if (Input.GetKeyUp(KeyCode.L))
            characterAnimator.SetTrigger("SphereAnimTrigger");
    }
    
    void OnTriggerEnter(Collider destroy_player)
    {
        Debug.Log(destroy_player.gameObject);
        if (destroy_player.gameObject.CompareTag("DestroyEnemy"))
        {
            tickSource.Play();
            Destroy(destroy_player.gameObject);
        }
    }


}
Все скрипты проекта представлены на рисунке 8.
 
Рисунок 8 – Скрипты проекта
