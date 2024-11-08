# Aula 14 - Criação de fases

## 🗺️ Roadmap
- [x] Configurando ambiente
- [x] Iniciando configuração do jogador
- [x] Terminando cenário
- [x] Criando a lógica inicial das moedas
- [x] Criando bombas que destroem o jogador
- [x] Criar fases

## 🔧 Project SetUp

- [x] Renomear o arquivo Sample Scene para Fase 1
- [x] Apertar ctrl+D para duplicar a fase
- [x] Mudar o piso da fase para laranja
- [x] Alterar o game manager para saber quando todas as moedas foram coletadas e carregar a práxima fase
- [x] Alterar o Building Settings
- [x] Criar a fase 3 utilizando o Ctrl+D e fazer diferenciação das fases
- [x] Criar opção de recomeçar fase

## 📝 Scripts
### GameManager
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class GameManager : MonoBehaviour
{
    public int moedasFase;
    public AudioSource somMoeda;
    public string nomeProximaFase;

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
        if(moedasFase <= 0){
            SceneManager.LoadScene(nomeProximaFase);
        }
    }

    public void reiniciarPartida(){
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
    }
}
```
