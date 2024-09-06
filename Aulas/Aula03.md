# Aula 3 - Setup do personagem

## 🗺️ Roadmap - Caça ao Tesouro

   - [x] Criar o jogo
   - [x] Display do personagem
   - [x] Controle do personagem
   - [ ] Display background tiles
   - [ ] Display objects

## 🔧 Project SetUp

   - [x] Criar pacote chamado entity e dentro dele uma classe chamada Entity
   - [x] Criar classe Player
   - [x] Instanciar o player no GamePanel
   - [x] Passar o update e draw para a classe player
   - [x] Criar pacote player e colocar imagens
      - [x]  [Sprites Andando.zip](https://github.com/user-attachments/files/16901504/Sprites.Andando.zip)
   - [x] Colocar sprites na interface
   - [x] Criar animação

### Entity

``` java

package entity;

import java.awt.image.BufferedImage;


public class Entity {
    
    public int x, y;
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
import java.awt.Color;
import java.awt.Graphics2D;
import java.awt.image.BufferedImage;
import java.io.IOException;
import javax.imageio.ImageIO;

public class Player extends Entity {

    GamePanel gp;
    KeyHandler tecla;

    public Player(GamePanel gp, KeyHandler tecla) {
        this.gp = gp;
        this.tecla = tecla;

        setDefaultValues();
        getImagemPlayer();
    }

    public void setDefaultValues() {
        x = 100;
        y = 100;
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
                y -= speed;
            } else if (tecla.downPressed == true) {
                direction = "down";
                y += speed;
            } else if (tecla.leftPressed == true) {
                direction = "left";
                x -= speed;
            } else if (tecla.rightPressed == true) {
                direction = "right";
                x += speed;
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

        g2.drawImage(image, x, y, gp.tileTamanho, gp.tileTamanho, null);
    }
}
```

### GamePanel
```Java
package entity;

import aula01cg.GamePanel;
import aula01cg.KeyHandler;
import java.awt.Color;
import java.awt.Graphics2D;
import java.awt.image.BufferedImage;
import java.io.IOException;
import javax.imageio.ImageIO;

public class Player extends Entity {

    GamePanel gp;
    KeyHandler tecla;

    public Player(GamePanel gp, KeyHandler tecla) {
        this.gp = gp;
        this.tecla = tecla;

        setDefaultValues();
        getImagemPlayer();
    }

    public void setDefaultValues() {
        x = 100;
        y = 100;
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
                y -= speed;
            } else if (tecla.downPressed == true) {
                direction = "down";
                y += speed;
            } else if (tecla.leftPressed == true) {
                direction = "left";
                x -= speed;
            } else if (tecla.rightPressed == true) {
                direction = "right";
                x += speed;
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

        g2.drawImage(image, x, y, gp.tileTamanho, gp.tileTamanho, null);
    }
}
```
