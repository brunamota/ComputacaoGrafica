# Aula 1 -  O mecanismo dos jogos 2D

## 🗺️ Roadmap - Cassa ao Tesouro

   - [x] Criar o jogo
   - [ ] Display do personagem
   - [ ] Controle do personagem
   - [ ] Display background tiles
   - [ ] Display objects

## Project SetUp

   - [x] Criar projeto chamado Game2D
   - [x] Criar classe Janela
   - [x] Criar classe GamePanel
   - [x] Configuração da tela do game 2D
   - [x] Implementar o Runnable

### Main
```Java
package aula01cg;

public class Aula01CG {

    public static void main(String[] args) {
        new Janela();
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
        
        gamePanel.startGameThread();
    
    }

}
```

### GamePanel
```Java

package aula01cg;

import java.awt.Color;
import java.awt.Dimension;
import javax.swing.JPanel;

public class GamePanel extends JPanel implements Runnable{
    
    //configurações da tela
    final int tileTamanhoOriginal = 16; //16x16 tile
    final int escala = 3;
    
    final int tileTamanho = tileTamanhoOriginal * escala; //48x48 tile
    
    //tela 4x3
    final int maxTelaColuna = 16; 
    final int maxTelaLinha = 12;
    final int telaLargura = tileTamanho * maxTelaColuna; //769 pixels
    final int telaAltura = tileTamanho * maxTelaLinha; //576 pixels
    
    Thread gameThread;
    
    public GamePanel(){
        this.setPreferredSize(new Dimension(telaLargura, telaAltura));
        this.setBackground(Color.black);
        this.setDoubleBuffered(true);
        
    }
    
    public void startGameThread(){
        gameThread = new Thread(this);
        gameThread.start();
    }
    

    @Override
    public void run() {
        while(gameThread != null){
            System.out.println("The game loop is running");
        }
        
    }
    
}
```
