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
     - [x] [mapa01.zip](https://github.com/user-attachments/files/16987751/mapa01.zip)
   - [x] Criar um loadMap() e chamar ele no construtor de TileManager

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
        mapTileNum = new int [gp.maxTelaColuna][gp.maxTelaLinha]; 
        getTileImage();
        loadMap("/mapa/map01.txt");
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
    
    public void loadMap(String filePath){
        try {
            InputStream is = getClass().getResourceAsStream(filePath);
            BufferedReader br = new BufferedReader(new InputStreamReader(is));
            
            int col = 0;
            int row = 0;
            while(col < gp.maxTelaColuna && row < gp.maxTelaLinha){
                String line = br.readLine();
                while(col < gp.maxTelaColuna){
                    String numbers[] = line.split(" ");
                    
                    int num = Integer.parseInt(numbers[col]);
                    
                    mapTileNum[col][row] = num;
                    col++;
                }
                if(col == gp.maxTelaColuna){
                    col = 0;
                    row++;
                }
            }
            br.close();
        } catch (Exception e) {
        }
    }
    
    public void draw(Graphics2D g2){
        int col = 0;
        int row = 0;
        int x = 0;
        int y = 0;
        
        while(col<gp.maxTelaColuna && row < gp.maxTelaLinha){
            
            int tileNum = mapTileNum[col][row];
            
            g2.drawImage(tile[tileNum].image, x, y, gp.tileTamanho, gp.tileTamanho, null);
            col++;
            x += gp.tileTamanho;
            if(col == gp.maxTelaColuna){
                col = 0;
                x = 0;
                row++;
                y+= gp.tileTamanho;
            }
        }
             
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
    
    //FPS
    public int FPS = 60;
    
    Thread gameThread;
    
    KeyHandler tecla = new KeyHandler();
    
    Player player = new Player(this, tecla);
    
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
