# Aula 13 - Criando cenas de reload
## 🗺️ Roadmap
- [x] Configurando ambiente da IDE e Engine
- [x] Iniciando configuração do jogador
- [x] Configuração de cenário
- [x] Criando a lógica de coletáveis
- [x] Criando cenas de reload
- [x] Criando tela de menu
- [ ] Baixando Assets para deixar o jogo mais bonito

## 🔧 Project SetUp

- [x] Criar uma pasta PreFab
- [x] Arrastar o gameobject moeda para a pasta
- [x] Criar um game object do tipo sphere com o nome de Bomba:
    - Posição → X: 2, Y: 0.5, Z: 4
    - Escala → X: 0.5, Y: 0.5, Z: 0.5
- [x] Criar um material da cor Roxa
- [x] No GameManager importar o 'using UnityEngine.SceneManagement;'
- [x] Criar a variável somMoeda do tipo AudioSource
- [x] Criar método reiniciarPartida()
- [x] Criar um script chamado Bombas
- [x] Arrastar script para o game object bomba
- [x] Criar prefabs para o Jogador e Bomba

## 📝 Scripts
### GameManager

``` C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class GameManager : MonoBehaviour
{
    public int moedasFase;
    public AudioSource somMoeda;

    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }

    public void descontarMoedas(){
        moedasFase -= 1;
        somMoeda.Play();
    }

    public void reiniciarPartida(){
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
    }
}
```

### Bombas

``` C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Bombas : MonoBehaviour
{
    void OnTriggerEnter(Collider other){
        if(other.gameObject.CompareTag("Player")){
            FindObjectOfType<GameManager>().reiniciarPartida();
            Destroy(this.gameObject);
        }

    }
}
```
