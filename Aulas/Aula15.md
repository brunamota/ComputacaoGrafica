# Aula 15 - Tela de Menu

## 🗺️ Roadmap
- [x] Configurando ambiente
- [x] Iniciando configuração do jogador
- [x] Terminando cenário
- [x] Criando a lógica inicial das moedas
- [x] Criando bombas que destroem o jogador
- [x] Criando tela de menu

## 🔧 Project SetUp

- [x] Criar uma pasta chamada Canva e colocar os itens dentro
    -  [Menu.zip](https://github.com/user-attachments/files/17740465/Menu.zip)
- [x] Criar uma cena nova chamada Menu
- [x] Criar um Canva utilziando UI
- [x] Mudar o Canvas Scaler para Scale With Screen Size e mudar resolução para 1920x1080
- [x] Criar um Painel
- [x] Colocar o BackGround e colocar o Alpha no 255
- [x] Criar um botão, colocar o tamanho dele em 700x200
- [x] Colocar a imagem do Sprite
- [x] Mudar o texto para Começar, tamanho 80 e colocar a fonte Montserrat
- [x] Mudar a cor de highlighted colocar para D9A1FB e Fade Duration para 0.3
- [x] Fazer isso para 2 botões: Começar e Sair
- [x] Colocar cada botão nas posições:
    - Começar → X: 0, Y: -25, Z: 0
    - Sair → X: 0, Y: -275, Z: 0
- [x] Criar um objeto vazio dentro do canva chamado MenuPrincipal, mudar o ponto de ancoragem para o centro e zerar todas as posiçoes
- [x] Arrastar os botões criados para dentro do MenuPrincipal
- [x] Criar um Script MenuManager para funcionar os botões
- [x] Criar um objeto vazio chamado MenuManager e arrastar o script
- [x] Configurar botões com On Click ()

## 📝 Scripts
### MenuManager

``` C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class MenuManager : MonoBehaviour
{
    public string nomeProximaFase;
    public GameObject painelMenu;
    public GameObject painelOpcoes;

    public void Começar(){
        SceneManager.LoadScene(nomeProximaFase);
    }

     public void Sair(){
        Application.Quit();
    }
}
```
