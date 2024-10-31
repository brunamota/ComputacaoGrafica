# Aula 11 - Primeiros passos para criar um jogo 3D usando a Unity

## 🗺️ Roadmap
- [x] Configurando ambiente
- [x] Iniciando configuração do jogador

## 🔧 Project SetUp

 - [x] Baixar a Unity - https://unity.com/pt
 - [x] Instalar a versão 2022.3.51f1, selecionar Windows Build Support (IL2CPP) e desmarcar a opção de baixar o Visual Studio pela Unity
 - [x] Baixar o VSCode - https://code.visualstudio.com/
 - [ ] Baixar extensão Unity
 - [x] Crie um novo projeto chamado "Jogo3D" do tipo 3D (Built-in Render Pipeline)
 - [x] Colocar as pastas Ícone e Efeito Sonoro na Pasta Assets
     - [Assets.zip](https://github.com/user-attachments/files/17593588/Assets.zip)
 - [x] Verificar se aba game está marcada em Full HD
 - [x] Na aba Hierarchy, criar um 3D object do tipo Sphere
 - [x] Renomear para Jogador
 - [x] Na aba Inspector, clicar no botão Add Component e adicionar um RigidBody
 - [x] Na aba Hierarchy, criar um 3D object do tipo Cube, colocar na posição 0,0,0 e de escala 20, 0.1, 20
 - [x] Criar um pasta dentro de project chamada Scripts
 - [x] Criar um script chamado ControleDoJogador

### ControleDoJogador

``` C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ControleDoJogador : MonoBehaviour
{

    public float velocidadeDoJogador;
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
