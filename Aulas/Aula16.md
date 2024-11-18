# Aula 16 - Usando a Assets Store

## 🗺️ Roadmap
- [x] Configurando ambiente
- [x] Iniciando configuração do jogador
- [x] Terminando cenário
- [x] Criando a lógica inicial das moedas
- [x] Criando bombas que destroem o jogador
- [x] Criando tela de menu
- [x] Baixando Assets para deixar o jogo mais bonito

## 🔧 Project SetUp

- [x] 

## 📝 Scripts
### Controle do Jogador
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ControleDoJogador : MonoBehaviour
{

    public float velocidadeDoJogador;
    public Rigidbody rigidBody;
    private float movimentoX;
    private float movimentoZ;
    public Animator animator;

    // Start is called before the first frame update
    void Start()
    {
        
    }

    void Update()
    {

        movimentoBola();

        if(Input.GetKey(KeyCode.A)){
            this.transform.rotation = Quaternion.Euler(0,-90,0);
            animator.SetBool("walk", true);
        }
        else if(Input.GetKey(KeyCode.D)){
            this.transform.rotation = Quaternion.Euler(0,90,0);
            animator.SetBool("walk", true);
        }
        else if(Input.GetKey(KeyCode.W)){
            this.transform.rotation = Quaternion.Euler(0,0,0);
            animator.SetBool("walk", true);
        }
        else if(Input.GetKey(KeyCode.S)){
            this.transform.rotation = Quaternion.Euler(0,180,0);
            animator.SetBool("walk", true);
        } else{
            animator.SetBool("walk", false);
        }
    }

    private void FixedUpdate()
    {
        // Aplique a força no FixedUpdate
        Vector3 movimento = new Vector3(movimentoX, 0.0f, movimentoZ);
        rigidBody.AddForce(movimento, ForceMode.VelocityChange);
    }

    private void movimentoBola()
    {
        movimentoX = Input.GetAxis("Horizontal") * velocidadeDoJogador;
        movimentoZ = Input.GetAxis("Vertical") * velocidadeDoJogador;
    }
}
```
