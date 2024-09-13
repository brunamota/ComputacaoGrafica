# Aula 4 - Criando fundo baseados nos tiles

## 🗺️ Roadmap - Caça ao Tesouro

   - [x] Criar o jogo
   - [x] Display do personagem
   - [x] Controle do personagem
   - [x] Display background tiles
   - [ ] Display objects

## 🔧 Project SetUp

   - [x] Criar pacote chamado fundo e dentro dele colocar as imagens
     - [x] [Tile Fundo.zip](https://github.com/user-attachments/files/16987447/Tile.Fundo.zip)
   - [x] Criar pacote tile e criar uma classe chamada Tile e outra chamada TileManager
   - [x] Instaciar o TileManager no GamePanel
   - [x] Criar o mapa de tile em um arquivo txt
     - [x] [Uploadin1 0 0 1 1 1 1 1 1 1 1 1 1 1 1 1
1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
1 0 0 0 0 0 2 2 2 2 0 0 0 0 0 1
1 0 0 0 0 0 2 2 2 2 0 0 0 0 0 1
1 0 0 0 0 0 2 2 2 2 0 0 0 0 0 1
1 0 0 0 0 0 2 2 2 2 0 0 0 0 0 1
1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1g mapa01.txt…]()

   - [x] Criar um loadMap()
   - [x] Criar pacote player e colocar imagens
      - [x]  [Sprites Andando.zip](https://github.com/user-attachments/files/16901504/Sprites.Andando.zip)
   - [x] Colocar sprites na interface
   - [x] Criar animação

### Tile

``` java
package tiles;

import java.awt.image.BufferedImage;


public class Tile {
    public BufferedImage image;
    public boolean collision = false;
}

```

### TileManager

``` java

package tiles;

import aula01cg.GamePanel;
import java.awt.Graphics2D;
import java.io.IOException;
import javax.imageio.ImageIO;

public class TileManager {
    GamePanel gp;
    Tile[] tile;

    public TileManager(GamePanel gp) {
        this.gp = gp;
        tile = new Tile[10];
        getTileImage();
    }
    
    public void getTileImage(){
        try {
            tile[0] = new Tile();
            tile[0].image = ImageIO.read(getClass().getResourceAsStream("/Fundo/grass.png"));
            tile[1] = new Tile();
            tile[1].image = ImageIO.read(getClass().getResourceAsStream("/Fundo/wall.png"));
            tile[2] = new Tile();
            tile[2].image = ImageIO.read(getClass().getResourceAsStream("/Fundo/water.png"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    public void draw(Graphics2D g2){
        g2.drawImage(tile[0].image, 0, 0, gp.tileTamanho, gp.tileTamanho, null);
        g2.drawImage(tile[1].image, 48, 0, gp.tileTamanho, gp.tileTamanho, null);
        g2.drawImage(tile[2].image, 2*48, 0, gp.tileTamanho, gp.tileTamanho, null);
    }
       
}

```

### GamePanel
```Java

package aula01cg;

import entity.Player;
import java.awt.Color;
import java.awt.Dimension;
import java.awt.Graphics;
import java.awt.Graphics2D;
import javax.swing.JPanel;

public class GamePanel extends JPanel implements Runnable{
    
    //configurações da tela
    public final int tileTamanhoOriginal = 16; //16x16 tile
    public final int escala = 3;
    
    public final int tileTamanho = tileTamanhoOriginal * escala; //48x48 tile
    
    //tela 4x3
    public final int maxTelaColuna = 16; 
    public final int maxTelaLinha = 12;
    public final int telaLargura = tileTamanho * maxTelaColuna; //769 pixels
    public final int telaAltura = tileTamanho * maxTelaLinha; //576 pixels
    
    //FPS
    public int FPS = 60;
    
    Thread gameThread;
    
    KeyHandler tecla = new KeyHandler();
    
    Player player = new Player(this, tecla);
    
    //Setar local padrão do jogador
    int playerX = 100;
    int playerY = 100;
    int playerSpeed = 4;
    
    public GamePanel(){
        this.setPreferredSize(new Dimension(telaLargura, telaAltura));
        this.setBackground(Color.black);
        this.setDoubleBuffered(true);
        
        this.addKeyListener(tecla);
        this.setFocusable(true);
        
    }
    
    public void startGameThread(){
        gameThread = new Thread(this);
        gameThread.start();
    }
    

    @Override
    public void run() {
        
        double drawInterval = 1000000000/FPS; //0,016 taxa de atualização
        double delta = 0;
        long ultimoTempo = System.nanoTime();
        long tempoAtual;
        long timer = 0;
        long drawCount = 0;
        
        while(gameThread != null){   
            tempoAtual = System.nanoTime();
            delta += (tempoAtual - ultimoTempo)/drawInterval;
            timer += (tempoAtual - ultimoTempo);
            ultimoTempo = tempoAtual;
            
            if(delta >= 1){
            //Atualizar informações, posição do jogador
            update();
            //Desenhar a tela com a informação atualizada
            repaint();
            delta--;
            drawCount++;
            }
            
            if(timer >= 1000000000){
                System.out.println("FPS: " + drawCount);
                drawCount = 0;
                timer = 0;
            }
        }
        
       
    }
    
    public void update(){
        player.update();
    }

    
    @Override
    public void paintComponent(Graphics g){
        //metodo do JPanel
        super.paintComponent(g);
        
        Graphics2D g2 = (Graphics2D) g;
       
        player.draw(g2);
        
        g2.dispose();
    }
    
}
```
