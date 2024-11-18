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

- [x] Baixar o Asset na Unity: [Player](https://assetstore.unity.com/packages/3d/characters/quarter-view-3d-action-assets-pack-188720)
- [x] Importar apenas a pasta Material, Models, Prefabs e Textures
- [x] Selecionar Luna, Ludo ou Player
- [x] Arrastar para o centro do jogo e ajusta:
      - Posição → X: 0, Y: 0.5, Z: 0
      - Escala → X: 0.5, Y: 0.5, Z: 0.5
- [x] Adicionar Capsule Collider, ajustar para o tamanho do boneco escolhido
- [x] Adicionar RigidBody e em Constraints selecionar caixas do X e Y do Freeze Rotation
- [x] Adicionar um Animator
- [x] Criar um Controller para criar a animação
- [ ] Criar um Avatar para o boneco
- [ ] Desativar o 
- [x] Na pasta Models, selecionar personagem escolhido, criar avatar e pegar as animações de idle e walk/run
- [x] Mudar tag do boneco para "Player" e adicionar script Controle do Jogador (fazer mudanças no script para a rotação funcionar)
- [x] Colocar RigidBody e Animator
- [x] Adicionar moeda do pacote de Assets
      - Escala → X: 0.3, Y: 0.3, Z: 0.3
- [x] Adicionar uma rotação no script

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

### Moedas
``` C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Moedas : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        FindObjectOfType<GameManager>().moedasFase += 1;
    }

    void Update()
    {
        transform.Rotate(new Vector3(0,45,0) * Time.deltaTime);
    }

    void OnTriggerEnter(Collider other){
        if(other.gameObject.CompareTag("Player")){
            FindObjectOfType<GameManager>().descontarMoedas();
            Destroy(this.gameObject);
        }

    }

}
```

#### Bombas

```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Bombas : MonoBehaviour
{
    public Animator animator;
    
    void OnTriggerEnter(Collider other){
        if(other.gameObject.CompareTag("Player")){
            animator.SetTrigger("attack01");
            StartCoroutine(tempoExplosao());
        }
    }

    IEnumerator tempoExplosao()
    {
        yield return new WaitForSeconds(1.0f);
        FindObjectOfType<GameManager>().reiniciarPartida();
    }
}
```
