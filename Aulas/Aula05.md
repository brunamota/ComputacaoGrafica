# Aula 5 - Mapa e Câmera do personagem

## 🗺️ Roadmap - Caça ao Tesouro

   - [x] Criar o jogo
   - [x] Display do personagem
   - [x] Controle do personagem
   - [x] Display background tiles
   - [x] Câmera do personagem
   - [ ] Colisões
   - [ ] Display objects

## 🔧 Project SetUp

   - [x] Criar o restante das imagens no TileManager
   - [x] Colocar um novo mapa 50x50 e mudar o diretorio no TileManager
     - [x] [world01.zip](https://github.com/user-attachments/files/17063552/world01.zip)
   - [x] Mudar as variáveis x e y da classe Entity e na classe Player
   - [x] Setar nova posição para o Player baseada no novo mapa.
   - [x] Criar variáveis para a posição da tela e chamar no construtor
   - [x] Criar parâmetros para a mapa no GamePanel
   - [x] Mudar valores no mapTileNum no Tile Manager
   - [x] Mudar o path do loadMap() no construtor
   - [x] Mudar variaveis do método loadMap()
   - [x] Atualizar o draw() no Tile Manager

### TileManager

``` java

package tiles;

import aula01cg.GamePanel;
import java.awt.Graphics2D;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import javax.imageio.ImageIO;

public class TileManager {

    GamePanel gp;
    Tile[] tile;
    int mapTileNum[][];

    public TileManager(GamePanel gp) {
        this.gp = gp;
        tile = new Tile[10];
        mapTileNum = new int[gp.maxMundoColuna][gp.maxMundoLinha];
        getTileImage();
        loadMap("/mapa/world01.txt");
    }

    public void getTileImage() {
        try {
            tile[0] = new Tile();
            tile[0].image = ImageIO.read(getClass().getResourceAsStream("/Fundo/grass.png"));
            tile[1] = new Tile();
            tile[1].image = ImageIO.read(getClass().getResourceAsStream("/Fundo/wall.png"));
            tile[2] = new Tile();
            tile[2].image = ImageIO.read(getClass().getResourceAsStream("/Fundo/water.png"));
            tile[3] = new Tile();
            tile[3].image = ImageIO.read(getClass().getResourceAsStream("/Fundo/earth.png"));
            tile[4] = new Tile();
            tile[4].image = ImageIO.read(getClass().getResourceAsStream("/Fundo/tree.png"));
            tile[5] = new Tile();
            tile[5].image = ImageIO.read(getClass().getResourceAsStream("/Fundo/sand.png"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void loadMap(String filePath) {
        try {
            InputStream is = getClass().getResourceAsStream(filePath);
            BufferedReader br = new BufferedReader(new InputStreamReader(is));

            int col = 0;
            int row = 0;
            while (col < gp.maxMundoColuna && row < gp.maxMundoLinha) {
                String line = br.readLine();
                while (col < gp.maxMundoColuna) {
                    String numbers[] = line.split(" ");

                    int num = Integer.parseInt(numbers[col]);

                    mapTileNum[col][row] = num;
                    col++;
                }
                if (col == gp.maxMundoColuna) {
                    col = 0;
                    row++;
                }
            }
            br.close();
        } catch (Exception e) {
        }
    }

    public void draw(Graphics2D g2) {
        int worldCol = 0;
        int worldRow = 0;

        while (worldCol < gp.maxMundoColuna && worldRow < gp.maxMundoLinha) {

            int tileNum = mapTileNum[worldCol][worldRow];

            int worldX = worldCol * gp.tileTamanho;
            int worldY = worldRow * gp.tileTamanho;
            int screenX = worldX - gp.player.worldX + gp.player.screenX;
            int screenY = worldY - gp.player.worldY + gp.player.screenY;
 
                g2.drawImage(tile[tileNum].image, screenX, screenY, gp.tileTamanho, gp.tileTamanho, null);
            worldCol++;
            if (worldCol == gp.maxMundoColuna) {
                worldCol = 0;
                worldRow++;
            }
        }
    }

}
```
### Entity
```Java

package entity;

import java.awt.image.BufferedImage;


public class Entity {
    
    public int worldX, worldY;
    public int speed;
    
    public BufferedImage up1, up2;
    public BufferedImage down1, down2;
    public BufferedImage left1, left2;
    public BufferedImage right1, right2;
    
    public String direction;
    
    public int spriteCouner;
    public int spriteNum = 1;
}

```

### Player
```Java
package entity;

import aula01cg.GamePanel;
import aula01cg.KeyHandler;
import java.awt.Graphics2D;
import java.awt.image.BufferedImage;
import java.io.IOException;
import javax.imageio.ImageIO;

public class Player extends Entity {

    GamePanel gp;
    KeyHandler tecla;
    
    public final int screenX;
    public final int screenY;

    public Player(GamePanel gp, KeyHandler tecla) {
        this.gp = gp;
        this.tecla = tecla;
        
        screenX = gp.telaLargura/2 - (gp.tileTamanho/2);
        screenY = gp.telaAltura/2 - (gp.tileTamanho/2);

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
                worldY -= speed;
            } else if (tecla.downPressed == true) {
                direction = "down";
                worldY += speed;
            } else if (tecla.leftPressed == true) {
                direction = "left";
                worldX -= speed;
            } else if (tecla.rightPressed == true) {
                direction = "right";
                worldX += speed;
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
### GamePanel

```Java

package aula01cg;

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
    
    public Player player = new Player(this, tecla);
    
    //Setar local padrão do jogador
    int playerX = 100;
    int playerY = 100;
    int playerSpeed = 4;
    
    
    TileManager tileM = new TileManager(this);
    
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
        
        tileM.draw(g2);
        player.draw(g2);
        
        g2.dispose();
    }
    
}
```
