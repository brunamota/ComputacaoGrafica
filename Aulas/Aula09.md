# Aula 9 - Trilha sonora

## 🗺️ Roadmap - Caça ao Tesouro

   - [x] Configurações da tela do jogo
   - [x] Criando um game loop (60 FPS)
   - [x] Display e controle do personagem
   - [x] Display background tiles
   - [x] Câmera do personagem
   - [x] Colisões
   - [x] Display de objetos
   - [x] Interação com objetos
   - [x] Trilha sonora

## 🔧 Project SetUp

   - [x] Criar dentro do config_object uma classe chamada ObjectBoot
   - [x] Instanciar a bota no AssetSetter
   - [x] Criar um case para a Bota na pickUpObject() na classe Player
   - [x] Criar um pacote chamado musica
      - [x] [Musicas.zip](https://github.com/user-attachments/files/17335188/Musicas.zip)
   - [x] Criar a classe Musica no pacote principal
   - [x] Ir para o GamePanel e instanciar a classe Musica
   - [x] Criar 3 metodos dentro de GamePanel
   - [x] Chamar método playMusica() no setUpGame()
   - [x] Chamar método playSE() no pickUpObject()

### ObjectBoot
``` java
package config_object;

import java.io.IOException;
import javax.imageio.ImageIO;

public class ObjectBoot extends SuperObject {

    public ObjectBoot() {
        name = "Boots";
        try {
            image = ImageIO.read(getClass().getResourceAsStream("/objetos/boots.png"));
        } catch (IOException e) {
        }
    }
}

```
### AssetSetter
```Java

package aula01cg;

import config_object.ObjectBoot;
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
        
        gp.obj[7] = new ObjectBoot();
        gp.obj[7].worldX = 37 * gp.tileTamanho;
        gp.obj[7].worldY = 42 * gp.tileTamanho;
        
    }   
}

```
### Player

```Java
package entity;

import aula01cg.GamePanel;
import aula01cg.KeyHandler;
import java.awt.Graphics2D;
import java.awt.Rectangle;
import java.awt.image.BufferedImage;
import java.io.IOException;
import javax.imageio.ImageIO;

public class Player extends Entity {

    GamePanel gp;
    KeyHandler tecla;

    public final int screenX;
    public final int screenY;
    
    int hasKey = 0;
    

    public Player(GamePanel gp, KeyHandler tecla) {
        this.gp = gp;
        this.tecla = tecla;

        screenX = gp.telaLargura / 2 - (gp.tileTamanho / 2);
        screenY = gp.telaAltura / 2 - (gp.tileTamanho / 2);
       
        solidArea = new Rectangle(8,16,32,32);
        
        solidAreaDefaultX = solidArea.x;
        solidAreaDefaultY = solidArea.y;     

        setDefaultValues();
        getImagemPlayer();
    }

    public void setDefaultValues() {
        worldX = gp.tileTamanho * 23;
        worldY = gp.tileTamanho * 21;
        speed = 4;
        direction = "down";
    }

    public void getImagemPlayer() {
        try {
            up1 = ImageIO.read(getClass().getResourceAsStream("/player/boy_up_1.png"));
            up2 = ImageIO.read(getClass().getResourceAsStream("/player/boy_up_2.png"));
            down1 = ImageIO.read(getClass().getResourceAsStream("/player/boy_down_1.png"));
            down2 = ImageIO.read(getClass().getResourceAsStream("/player/boy_down_2.png"));
            left1 = ImageIO.read(getClass().getResourceAsStream("/player/boy_left_1.png"));
            left2 = ImageIO.read(getClass().getResourceAsStream("/player/boy_left_2.png"));
            right1 = ImageIO.read(getClass().getResourceAsStream("/player/boy_right_1.png"));
            right2 = ImageIO.read(getClass().getResourceAsStream("/player/boy_right_2.png"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void update() {

        if (tecla.upPressed == true || tecla.downPressed == true || tecla.leftPressed == true || tecla.rightPressed == true) {

            if (tecla.upPressed == true) {
                direction = "up";
            } else if (tecla.downPressed == true) {
                direction = "down";
            } else if (tecla.leftPressed == true) {
                direction = "left";
            } else if (tecla.rightPressed == true) {
                direction = "right";
            }

            collisionOn = false;
            gp.cChecker.checkTile(this);
            int objIndex = gp.cChecker.checkObject(this, true);
            pickUpObject(objIndex);
                        
            if (collisionOn == false) {
                switch (direction) {
                    case "up":
                        worldY -= speed;
                        break;
                    case "down":
                        worldY += speed;
                        break;
                    case "left":
                        worldX -= speed;
                        break;
                    case "right":
                        worldX += speed;
                        break;
                }
            }

            spriteCouner++;
            if (spriteCouner > 15) {
                if (spriteNum == 1) {
                    spriteNum = 2;
                } else if (spriteNum == 2) {
                    spriteNum = 1;
                }
            }
        }

    }
    
    public void pickUpObject(int i){
        if(i != 999){
            String objecName = gp.obj[i].name;
            
            switch (objecName) {
                case "Key":
                    gp.playSE(1);
                    hasKey++;
                    gp.obj[i] = null;
                    System.out.println("Key: " + hasKey);
                    break;
                case "Door":
                    if(hasKey > 0){
                        gp.playSE(3);
                        gp.obj[i] = null;
                        hasKey--;
                    }
                    break;
                case "Boots":
                    gp.playSE(2);
                    speed += 2;
                    gp.obj[i] = null;
                    break;
            }
        }
    }

    public void draw(Graphics2D g2) {
        BufferedImage image = null;

        switch (direction) {
            case "up":
                if (spriteNum == 1) {
                    image = up1;
                }
                if (spriteNum == 2) {
                    image = up2;
                }
                break;
            case "down":
                if (spriteNum == 1) {
                    image = down1;
                }
                if (spriteNum == 2) {
                    image = down2;
                }
                break;
            case "left":
                if (spriteNum == 1) {
                    image = left1;
                }
                if (spriteNum == 2) {
                    image = left2;
                }
                break;
            case "right":
                if (spriteNum == 1) {
                    image = right1;
                }
                if (spriteNum == 2) {
                    image = right2;
                }
                break;
        }

        g2.drawImage(image, screenX, screenY, gp.tileTamanho, gp.tileTamanho, null);
    }
}
```

### Musica

```Java

package aula01cg;

import java.net.URL;
import javax.sound.sampled.AudioInputStream;
import javax.sound.sampled.AudioSystem;
import javax.sound.sampled.Clip;

public class Musica {
    Clip clip;
    URL soundURL[] = new URL[30];

    public Musica() {
        soundURL[0] = getClass().getResource("/musica/BlueBoyAdventure.wav");
        soundURL[1] = getClass().getResource("/musica/coin.wav");
        soundURL[2] = getClass().getResource("/musica/powerup.wav");
        soundURL[3] = getClass().getResource("/musica/unlock.wav");
        soundURL[4] = getClass().getResource("/musica/fanfare.wav");
        
    }
    
    public void setFile(int i){
        try {
            AudioInputStream ais = AudioSystem.getAudioInputStream(soundURL[i]);
            clip = AudioSystem.getClip();
            clip.open(ais);
        } catch (Exception e) {
        }
    
    }
    
    public void play(){
        clip.start();
    }
    
    public void loop(){
        clip.loop(Clip.LOOP_CONTINUOUSLY);
    }
    
    public void stop(){
        clip.stop();
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
    
    Musica musica = new Musica();
    
    KeyHandler tecla = new KeyHandler();
    public CollisionChecker cChecker = new CollisionChecker(this);
    public AssetSetter aSetter = new AssetSetter(this);
    
    Thread gameThread;
    
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
        
        playMusica(0);
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
    
    public void playMusica(int i){
        musica.setFile(i);
        musica.play();
        musica.loop();
    }
    
    public void stopMusic(){
        musica.stop();
    }
    
    public void playSE(int i){
        musica.setFile(i);
        musica.play();
    }
    
}
```

