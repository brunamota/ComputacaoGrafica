# Aula 10 - Finalização do Jogo 🥳

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
   - [x] Finalizar o Jogo

## 🔧 Project SetUp

   - [x] Criar uma nova classe chamada UI no pacote principal
   - [x] Chamar o draw no paintComponent() do GamePanel
   - [x] Ir na classe player no método pickUpObject() e chamar a mensagem na Key, Door e Boots
   - [x] Criar um case para o Chest
         
### UI
``` java
package aula01cg;

import config_object.ObjectKey;
import java.awt.Color;
import java.awt.Font;
import java.awt.Graphics2D;
import java.awt.image.BufferedImage;
import java.text.DecimalFormat;

public class UI {

    GamePanel gp;
    Font arial_40, arial_80;
    BufferedImage keyImage;

    public boolean messageOn = false;
    public String message = "";
    public int messageCounter = 0;

    public boolean gameFinished = false;
    
    double playTime;
    DecimalFormat dFormat = new DecimalFormat("#0.00");

    public UI(GamePanel gp) {
        this.gp = gp;
        arial_40 = new Font("Arial", Font.PLAIN, 40);
        arial_80 = new Font("Arial", Font.BOLD, 80);
        ObjectKey key = new ObjectKey();
        keyImage = key.image;
    }

    public void showMessage(String text) {
        message = text;
        messageOn = true;
    }

    public void draw(Graphics2D g2) {

        if (gameFinished == true) {
            
            g2.setFont(arial_40);
            g2.setColor(Color.white);
            
            String text;
            int tamanhoTexto, x, y;
            
            text = "Você achou o tesouro!";
            tamanhoTexto = (int) g2.getFontMetrics().getStringBounds(text, g2).getWidth();
            x = gp.telaLargura/2 - tamanhoTexto/2;
            y = gp.telaAltura/2 - (gp.tileTamanho*3);           
            g2.drawString(text, x, y);
            
            text = "Seu tempo foi: " + dFormat.format(playTime) + "!";
            tamanhoTexto = (int) g2.getFontMetrics().getStringBounds(text, g2).getWidth();
            x = gp.telaLargura/2 - tamanhoTexto/2;
            y = gp.telaAltura/2 + (gp.tileTamanho*4);           
            g2.drawString(text, x, y);
            
            g2.setFont(arial_80);
            g2.setColor(Color.YELLOW);
            text = "Parabéns!";
            tamanhoTexto = (int) g2.getFontMetrics().getStringBounds(text, g2).getWidth();
            x = gp.telaLargura/2 - tamanhoTexto/2;
            y = gp.telaAltura/2 + (gp.tileTamanho*2);
            g2.drawString(text, x, y);
            
            gp.gameThread = null;
        } else {
            g2.setFont(arial_40);
            g2.setColor(Color.white);
            g2.drawImage(keyImage, gp.tileTamanho / 2, gp.tileTamanho / 2, gp.tileTamanho, gp.tileTamanho, null);
            g2.drawString("x " + gp.player.hasKey, 74, 65);
            
            playTime += (double)1/60;
            g2.drawString("Time: " + dFormat.format(playTime), gp.tileTamanho*11, 65);

            if (messageOn == true) {
                g2.setFont(g2.getFont().deriveFont(20F));
                g2.drawString(message, gp.tileTamanho / 2, gp.tileTamanho * 5);

                messageCounter++;
                if (messageCounter > 120) {
                    messageCounter = 0;
                    messageOn = false;
                }
            }
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
    
    Musica musica = new Musica();
    Musica efeito = new Musica();
    
    KeyHandler tecla = new KeyHandler();
    public CollisionChecker cChecker = new CollisionChecker(this);
    public AssetSetter aSetter = new AssetSetter(this);
    public UI ui = new UI(this);
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
        
        ui.draw(g2);
        
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
        efeito.setFile(i);
        efeito.play();
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

    
    public int hasKey = 0;
    

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
                    gp.ui.showMessage("Você pegou uma chave!");
                    break;
                case "Door":
                    if(hasKey > 0){
                        gp.playSE(3);
                        gp.obj[i] = null;
                        hasKey--;
                        gp.ui.showMessage("Você abriu uma porta!");
                    }else{
                        gp.ui.showMessage("Você precisa de uma chave!");
                    }
                    break;
                case "Boots":
                    gp.playSE(2);
                    speed += 2;
                    gp.obj[i] = null;
                    gp.ui.showMessage("Correee!");
                    break;
                case "Chest":
                    gp.ui.gameFinished = true;
                    gp.stopMusic();
                    gp.playSE(4);
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
