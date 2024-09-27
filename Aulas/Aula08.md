# Aula 8 - Interação com objetos

## 🗺️ Roadmap - Caça ao Tesouro

   - [x] Configurações da tela do jogo
   - [x] Criando um game loop (60 FPS)
   - [x] Display e controle do personagem
   - [x] Display background tiles
   - [x] Câmera do personagem
   - [x] Colisões
   - [x] Display de objetos
   - [x] Interação com objetos
   - [ ] Trilha sonora

## 🔧 Project SetUp

   - [x] Criar variaveis novas no Entity e inicia elas na classe Player
   - [x] Criar um retangulo para os objetos na classe SuperObject
   - [x] Setar collision para true na classe ObjectDoor e ObjectKey
   - [x] Criar o método checkObject() no CollisionChecker
   - [x] Chamar checkObject() no update() da classe Player
   - [x] Criar pickUpObject() no Player
         
### Entity
``` java
package entity;

import java.awt.Rectangle;
import java.awt.image.BufferedImage;


public class Entity {
    
    public int worldX, worldY;
    public int speed;
    
    public BufferedImage up1, up2;
    public BufferedImage down1, down2;
    public BufferedImage left1, left2;
    public BufferedImage right1, right2;
    
    public String direction;
    
    public int spriteCouner = 0;
    public int spriteNum = 1;
    
    public Rectangle solidArea;
    public int solidAreaDefaultX;
    public int solidAreaDefaultY;
    public boolean collisionOn = false;
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
                    hasKey++;
                    gp.obj[i] = null;
                    System.out.println("Key: " + hasKey);
                    break;
                case "Door":
                    if(hasKey > 0){
                        gp.obj[i] = null;
                        hasKey--;
                    }
                    break;
                case "Chest":
                    
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
### SuperObject

```Java
package config_object;

import aula01cg.GamePanel;
import java.awt.Graphics2D;
import java.awt.Rectangle;
import java.awt.image.BufferedImage;


public class SuperObject {
    
    public BufferedImage image;
    public String name;
    public boolean collision = false;
    public int worldX, worldY;
    public Rectangle solidArea = new Rectangle(0,0,48,48);
    public int solidAreaDefaultX = 0;
    public int solidAreaDefaultY = 0;
    
    
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

### ObjectDoor

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
        
        collision = true;
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
        collision = true;
    }
    
}

```

### CollisionChecker

```Java

package aula01cg;

import entity.Entity;


public class CollisionChecker {

    private GamePanel gp;

    public CollisionChecker(GamePanel gp) {
        this.gp = gp;
    }
    
    public void checkTile(Entity entity){
        int entityLeftWorldX = entity.worldX + entity.solidArea.x;
        int entityRightWorldX = entity.worldX + entity.solidArea.x + entity.solidArea.width;
        int entityTopWorldY = entity.worldY + entity.solidArea.y;
        int entityBottomWorldY = entity.worldY + entity.solidArea.y + entity.solidArea.height;
        
        int entityLeftCol = entityLeftWorldX/gp.tileTamanho;
        int entityRightCol = entityRightWorldX/gp.tileTamanho;
        int entityTopRow = entityTopWorldY/gp.tileTamanho;
        int entityBottomRow = entityBottomWorldY/gp.tileTamanho;
        
        int tileNum1, tileNum2;
        
        switch (entity.direction) {
            case "up":
                entityTopRow = (entityTopWorldY - entity.speed)/gp.tileTamanho;
                tileNum1 = gp.tileM.mapTileNum[entityLeftCol][entityTopRow];
                tileNum2 = gp.tileM.mapTileNum[entityRightCol][entityTopRow];
                if(gp.tileM.tile[tileNum1].collision == true ||gp.tileM.tile[tileNum2].collision == true){
                    entity.collisionOn = true;
                }
                break;
            case "down":
                entityBottomRow = (entityBottomWorldY + entity.speed)/gp.tileTamanho;
                tileNum1 = gp.tileM.mapTileNum[entityLeftCol][entityBottomRow];
                tileNum2 = gp.tileM.mapTileNum[entityRightCol][entityBottomRow];
                if(gp.tileM.tile[tileNum1].collision == true ||gp.tileM.tile[tileNum2].collision == true){
                    entity.collisionOn = true;
                }
                break;
            case "left":
                entityLeftCol = (entityLeftWorldX - entity.speed)/gp.tileTamanho;
                tileNum1 = gp.tileM.mapTileNum[entityLeftCol][entityTopRow];
                tileNum2 = gp.tileM.mapTileNum[entityLeftCol][entityBottomRow];
                if(gp.tileM.tile[tileNum1].collision == true ||gp.tileM.tile[tileNum2].collision == true){
                    entity.collisionOn = true;
                }
                break;
            case "right":
                entityRightCol = (entityRightWorldX + entity.speed)/gp.tileTamanho;
                tileNum1 = gp.tileM.mapTileNum[entityRightCol][entityTopRow];
                tileNum2 = gp.tileM.mapTileNum[entityRightCol][entityBottomRow];
                if(gp.tileM.tile[tileNum1].collision == true ||gp.tileM.tile[tileNum2].collision == true){
                    entity.collisionOn = true;
                }
                break;    
            
        }
    }
    
    public int checkObject(Entity entity, boolean player){
        int index = 999;
        
        for(int i = 0; i < gp.obj.length; i++){
            if(gp.obj[i] !=null){
               entity.solidArea.x = entity.worldX + entity.solidArea.x;
               entity.solidArea.y = entity.worldY + entity.solidArea.y;
               
               gp.obj[i].solidArea.x = gp.obj[i].worldX + gp.obj[i].solidArea.x;
               gp.obj[i].solidArea.y = gp.obj[i].worldY + gp.obj[i].solidArea.y;
               
               switch(entity.direction){
                   case "up":
                       entity.solidArea.y -= entity.speed;
                       if(entity.solidArea.intersects(gp.obj[i].solidArea)){
                           if(gp.obj[i].collision == true){
                               entity.collisionOn = true;
                           }
                           if(player == true){
                               index = i;
                           }
                       }
                       break;
                   case "down":
                       entity.solidArea.y += entity.speed;
                       if(entity.solidArea.intersects(gp.obj[i].solidArea)){
                           if(gp.obj[i].collision == true){
                               entity.collisionOn = true;
                           }
                           if(player == true){
                               index = i;
                           }
                       }
                       break;
                   case "left":
                       entity.solidArea.x -= entity.speed;
                       if(entity.solidArea.intersects(gp.obj[i].solidArea)){
                           if(gp.obj[i].collision == true){
                               entity.collisionOn = true;
                           }
                           if(player == true){
                               index = i;
                           }
                       }
                       break;
                   case "right":
                       entity.solidArea.x += entity.speed;
                       if(entity.solidArea.intersects(gp.obj[i].solidArea)){
                           if(gp.obj[i].collision == true){
                               entity.collisionOn = true;
                           }
                           if(player == true){
                               index = i;
                           }
                       }
                       break;
               }
            entity.solidArea.x = entity.solidAreaDefaultX;
            entity.solidArea.y = entity.solidAreaDefaultY;
            gp.obj[i].solidArea.x = gp.obj[i].solidAreaDefaultX;
            gp.obj[i].solidArea.y = gp.obj[i].solidAreaDefaultY;
            }      
        }
        return index;
    }
}

```
