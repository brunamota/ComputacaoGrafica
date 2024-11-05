# Aula 12 - Organizando o Cenário

## 🗺️ Roadmap
- [x] Configurando ambiente
- [x] Iniciando configuração do jogador

## 🔧 Project SetUp

- [x] Criar uma pasta chamada Materiais
- [x] Criar materiais com o nome Vermelho, Verde, Azul e Amarelo
- [x] Arrastar o vermelho para o Jogador e o verde para o Piso
- [x] Modificar a posição da câmera para a visão top down colocando:
    - Posição → X: 0, Y: 12, Z: -15 e Rotação → X: 45, Y: 0, Z: 0
- [x] Criar 4 paredes:
    - Posição → X: 0, Y: 0.45, Z: 10.5, Rotação → X: 0, Y: 0, Z: 0, Escala → X: 20, Y: 1, Z: 1
    - Posição → X: 0, Y: 0.45, Z: -10.5, Rotação → X: 0, Y: 0, Z: 0, Escala → X: 20, Y: 1, Z: 1
    - Posição → X: 10.5, Y: 0.45, Z: 0, Rotação → X: 0, Y: 90, Z: 0, Escala → X: 22, Y: 1, Z: 1
    - Posição → X: -10.5, Y: 0.45, Z: 0, Rotação → X: 0, Y: 90, Z: 0, Escala → X: 22, Y: 1, Z: 1
- [x] Arrastar o material para as 4 paredes
- [x] Criar um objeto 3D do tipo espera com nome Moeda e com Escala → X: 0.5, Y: 0.5, Z: 0.5
- [x] Colocar cor amarela no objeto Moeda
- [x] Dar um check no Is Triger do Colider da Moeda
- [x] Criar um Script chamado Moedas e outro chamado GameManager
- [x] Colocar a tag Player no objeto Jogador
- [x] Criar um objeto vazio para colocar o script GameManager
- [x] Colocar o script Moedas no objeto Moeda

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

    void OnTriggerEnter(Collider other){
        if(other.gameObject.CompareTag("Player")){
            Destroy(this.gameObject);
        }

    }

}
```

### GameManager

``` C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GameManager : MonoBehaviour
{
    public int moedasFase;

    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}
```
