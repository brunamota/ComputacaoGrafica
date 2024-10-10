# Aula 7 - Display de objetos

## 🗺️ Roadmap - Caça ao Tesouro

   - [x] Configurações da tela do jogo
   - [x] Criando um game loop (60 FPS)
   - [x] Display e controle do personagem
   - [x] Display background tiles
   - [x] Câmera do personagem
   - [x] Colisões
   - [x] Display de objetos
   - [ ] Interação com objetos
   - [ ] Trilha sonora
   - [ ] Finalizar o Jogo

## 🔧 Project SetUp

   - [x] Criar um pacote para os objetos
   - [x] Adicionar as imagens dos objetos dentro desse pacote
      -  [Objetos.zip](https://github.com/user-attachments/files/17155343/Objetos.zip)
   - [x] Criar classe SuperObject dentro do pacote config_object
   - [x] Criar classe ObjectKey dentro do pacote config_object
   - [x] Instaciar lista de objetos no GamePanel
   - [x] Criar classe AssetSetter dentro do package jogocg
   - [x] Instanciar AssetSetter no GamePanel
   - [x] Criar o setObject() no AssetSetter
   - [x] Criar o setUpGame() no GamePanel
   - [x] Chamar o setUpGame() na janela
   - [x] Implementar o draw no SuperObject
   - [x] Desenhar no GamePanel
   - [x] Criar outros objetos: Porta e Bau
         
### SuperObject
``` java

package config_object;

import java.awt.image.BufferedImage;


public class SuperObject {
    
    public BufferedImage image;
    public String name;
    public boolean collision = false;
    public int worldX, worldY;
    
   public void draw(Graphics2D g2, GamePanel gp){
        int screenX = worldX - gp.player.worldX + gp.player.screenX;
        int screenY = worldY - gp.player.worldY + gp.player.screenY;
 
            if(worldX + gp.tileTamanho > gp.player.worldX - gp.player.screenX &&
               worldX - gp.tileTamanho < gp.player.worldX + gp.player.screenX &&
               worldY + gp.tileTamanho > gp.player.worldY - gp.player.screenY &&
               worldY - gp.tileTamanho < gp.player.worldY + gp.player.screenY){
                
               g2.drawImage(image, screenX, screenY, gp.tileTamanho, gp.tileTamanho, null);
            }
    }
}

```

### ObjectKey
```Java

package config_object;

import java.io.IOException;
import javax.imageio.ImageIO;

public class ObjectKey extends SuperObject{

    public ObjectKey() {
        
        name = "Key";
        try {
            image = ImageIO.read(getClass().getResourceAsStream("/objetos/key.png"));
        } catch (IOException e) {
        }
    }
    
}

```
### GamePanel

```Java

package aula01cg;

import config_object.SuperObject;
import entity.Player;
import java.awt.Color;
import java.awt.Dimension;
import java.awt.Graphics;
import java.awt.Graphics2D;
import javax.swing.JPanel;
import tiles.TileManager;

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
    
    //Configurações do mundo
    public final int maxMundoColuna = 50;
    public final int maxMundoLinha = 50;
    public final int mundoLargura = tileTamanho * maxMundoColuna;
    public final int mundoAltura = tileTamanho * maxMundoLinha;
    
    //FPS
    public int FPS = 60;
    Thread gameThread;
    KeyHandler tecla = new KeyHandler();
    public CollisionChecker cChecker = new CollisionChecker(this);
    public AssetSetter aSetter = new AssetSetter(this);
    public Player player = new Player(this, tecla);
     
    
    //Setar local padrão do jogador
    int playerX = 100;
    int playerY = 100;
    int playerSpeed = 4;
    
    public SuperObject obj[] = new SuperObject[10];
    
    TileManager tileM = new TileManager(this);
    
    public GamePanel(){
        this.setPreferredSize(new Dimension(telaLargura, telaAltura));
        this.setBackground(Color.black);
        this.setDoubleBuffered(true);
        
        this.addKeyListener(tecla);
        this.setFocusable(true);
        
    }
    
    public void setUpGame(){
        aSetter.setObject();
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
        
        tileM.draw(g2);
        
        for(int i = 0; i < obj.length; i++){
            if(obj[i] != null){
                obj[i].draw(g2, this);
            }
        }
        
        player.draw(g2);
        
        g2.dispose();
    }
    
}

```

### AssetSetter

```Java

package aula01cg;

import config_object.ObjectChest;
import config_object.ObjectDoor;
import config_object.ObjectKey;


public class AssetSetter {
    
    GamePanel gp;

    public AssetSetter(GamePanel gp) {
        this.gp = gp;
    }
    
    public void setObject(){
        gp.obj[0] = new ObjectKey();
        gp.obj[0].worldX = 23 * gp.tileTamanho;
        gp.obj[0].worldY = 7 * gp.tileTamanho;
        
        gp.obj[1] = new ObjectKey();
        gp.obj[1].worldX = 23 * gp.tileTamanho;
        gp.obj[1].worldY = 40 * gp.tileTamanho;
        
        gp.obj[2] = new ObjectKey();
        gp.obj[2].worldX = 38 * gp.tileTamanho;
        gp.obj[2].worldY = 8 * gp.tileTamanho;
        
        gp.obj[3] = new ObjectDoor();
        gp.obj[3].worldX = 10 * gp.tileTamanho;
        gp.obj[3].worldY = 11 * gp.tileTamanho;
        
        gp.obj[4] = new ObjectDoor();
        gp.obj[4].worldX = 8 * gp.tileTamanho;
        gp.obj[4].worldY = 28 * gp.tileTamanho;
        
        gp.obj[5] = new ObjectDoor();
        gp.obj[5].worldX = 12 * gp.tileTamanho;
        gp.obj[5].worldY = 22 * gp.tileTamanho;
        
        gp.obj[6] = new ObjectChest();
        gp.obj[6].worldX = 10 * gp.tileTamanho;
        gp.obj[6].worldY = 7 * gp.tileTamanho;
        
    }
    
    
    
}

```

### Janela

```Java

package aula01cg;

import javax.swing.JFrame;

public class Janela extends JFrame{
  
    JFrame janela = new JFrame();
    
    Janela(){
        janela.setDefaultCloseOperation(EXIT_ON_CLOSE);
        janela.setResizable(false);
        janela.setTitle("Caça ao Tesouro");
        
        GamePanel gamePanel = new GamePanel();
        
        janela.add(gamePanel);
        
        janela.pack();
        
        janela.setLocationRelativeTo(null);
        janela.setVisible(true);
        
        gamePanel.setUpGame();
        
        gamePanel.startGameThread();
    
    }

}

```
#### ObjectDoor

```Java

package config_object;

import java.io.IOException;
import javax.imageio.ImageIO;


public class ObjectDoor extends SuperObject{
       
    public ObjectDoor() {
        
        name = "Door";
        try {
            image = ImageIO.read(getClass().getResourceAsStream("/objetos/door.png"));
        } catch (IOException e) {
        }
    }
}

```

#### ObjectChest

```Java

package config_object;

import java.io.IOException;
import javax.imageio.ImageIO;


public class ObjectChest extends SuperObject{
    
    public ObjectChest() {
        
        name = "Chest";
        try {
            image = ImageIO.read(getClass().getResourceAsStream("/objetos/chest.png"));
        } catch (IOException e) {
        }
    }
    
}
```
