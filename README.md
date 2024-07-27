# Java-Snake-Game

A simple Snake game implemented in Java using Swing for the GUI. The player controls a snake to eat food and grow longer while avoiding collisions with the walls and itself.

## Features

- Snake moves continuously in the direction set by the player.
- The snake grows longer each time it eats food.
- The game ends when the snake collides with the wall or itself.
- Displays the current score (number of apples eaten).
- "Restart" button to restart the game when it is over.
- "Exit" button to close the game.

## How to Run

1. Clone the repository or download the source code.
2. Open a terminal and navigate to the directory containing the source code files.
3. Compile the Java files using the following command:
   
 ## Source Code  

### RestartButtonHandler Code                  
            
    import javax.swing.JButton;
    import java.awt.event.ActionEvent;
    import java.awt.event.ActionListener;

    public class RestartButtonHandler implements ActionListener {

    private GamePanel gamePanel;
    private JButton restartButton;

    public RestartButtonHandler(GamePanel gamePanel, JButton restartButton) {
        this.gamePanel = gamePanel;
        this.restartButton = restartButton;
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        gamePanel.startGame();
    }
}

### SnakeGame Code                  
            
           public class SnakeGame {
             public static void main(String[] args) {
                  new GameFrame();
             }
           }


 
### ExitButtonHandler Code                  
            
           import java.awt.event.ActionEvent;
           import java.awt.event.ActionListener;

             public class ExitButtonHandler implements ActionListener {

                  @Override
                  public void actionPerformed(ActionEvent e) {
                  System.exit(0);
            }
          }




  
### GameFrame Code                  
            
            import javax.swing.JFrame;
            public class GameFrame extends JFrame {
            public GameFrame() {
                          this.add(new GamePanel());
                          this.setTitle("Snake Game");
                          this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
                          this.setResizable(false);
                          this.pack();
                          this.setVisible(true);
                          this.setLocationRelativeTo(null);
                     }
            }


### GamePanel Code 
```sh
import javax.swing.JPanel;
import javax.swing.Timer;
import javax.swing.JButton;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;
import java.util.Random;

public class GamePanel extends JPanel implements ActionListener {

    private static final int SCREEN_WIDTH = 600,SCREEN_HEIGHT = 600,UNIT_SIZE = 15,DELAY = 65;
    private static final int GAME_UNITS = (SCREEN_WIDTH * SCREEN_HEIGHT) / UNIT_SIZE;
    private final int[] x = new int[GAME_UNITS],y = new int[GAME_UNITS];
    private int bodyParts = 6,appleX,applesEaten,appleY;
    private char direction = 'R';
    private boolean running = false;
    private Timer timer;
    private Random random;
    private JButton restartButton,exitButton;

    public GamePanel() {
        random = new Random();
        this.setPreferredSize(new Dimension(SCREEN_WIDTH, SCREEN_HEIGHT));
        this.setBackground(Color.black);
        this.setFocusable(true);
        this.addKeyListener(new MyKeyAdapter());

        // Initialize the restart button
        restartButton = new JButton("Restart");
        restartButton.setBounds((SCREEN_WIDTH - 200) / 2, SCREEN_HEIGHT / 2 + 100, 100, 50);
        restartButton.addActionListener(new RestartButtonHandler(this, restartButton));
        this.setLayout(null);
        this.add(restartButton);
        restartButton.setVisible(false);

        // Initialize the exit button
        exitButton = new JButton("Exit");
        exitButton.setBounds((SCREEN_WIDTH + 50) / 2, SCREEN_HEIGHT / 2 + 100, 100, 50);
        exitButton.addActionListener(new ExitButtonHandler());
        this.add(exitButton);
        exitButton.setVisible(false);

        startGame();
    }

    public void startGame() {
        newApple();
        running = true;
        timer = new Timer(DELAY, this);
        timer.start();
        applesEaten = 0;
        bodyParts = 6;
        direction = 'R';
        for (int i = 0; i < bodyParts; i++) {
            x[i] = 0;
            y[i] = 0;
        }
        restartButton.setVisible(false); // Hide the button when the game starts
        exitButton.setVisible(false); // Hide the button when the game starts
    }

    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        draw(g);
    }

    public void draw(Graphics g) {
        if (running) {
            g.setColor(Color.red);
            g.fillOval(appleX, appleY, UNIT_SIZE, UNIT_SIZE);

            for (int i = 0; i < bodyParts; i++) {
                if (i == 0) {
                    g.setColor(Color.green);
                    g.fillRect(x[i], y[i], UNIT_SIZE, UNIT_SIZE);
                } else {
                    g.setColor(new Color(45, 180, 0));
                    g.fillRect(x[i], y[i], UNIT_SIZE, UNIT_SIZE);
                }
            }
            g.setColor(Color.red);
            g.setFont(new Font("Ink Free", Font.BOLD, 40));
            FontMetrics metrics = getFontMetrics(g.getFont());
            g.drawString("Score: " + applesEaten, (SCREEN_WIDTH - metrics.stringWidth("Score: " + applesEaten)) / 2, g.getFont().getSize());
        } else {
            gameOver(g);
        }
    }

    public void newApple() {
        appleX = random.nextInt((int) (SCREEN_WIDTH / UNIT_SIZE)) * UNIT_SIZE;
        appleY = random.nextInt((int) (SCREEN_HEIGHT / UNIT_SIZE)) * UNIT_SIZE;
    }

    public void move() {
        for (int i = bodyParts; i > 0; i--) {
            x[i] = x[i - 1];
            y[i] = y[i - 1];
        }

        switch (direction) {
            case 'U':
                y[0] = y[0] - UNIT_SIZE;
                break;
            case 'D':
                y[0] = y[0] + UNIT_SIZE;
                break;
            case 'L':
                x[0] = x[0] - UNIT_SIZE;
                break;
            case 'R':
                x[0] = x[0] + UNIT_SIZE;
                break;
        }
    }

    public void checkApple() {
        if ((x[0] == appleX) && (y[0] == appleY)) {
            bodyParts++;
            applesEaten++;
            newApple();
        }
    }

    public void checkCollisions() {
        for (int i = bodyParts; i > 0; i--) {
            if ((x[0] == x[i]) && (y[0] == y[i])) {
                running = false;
            }
        }

        if (x[0] < 0) {
            running = false;
        }

        if (x[0] > SCREEN_WIDTH) {
            running = false;
        }

        if (y[0] < 0) {
            running = false;
        }

        if (y[0] > SCREEN_HEIGHT) {
            running = false;
        }

        if (!running) {
            timer.stop();
        }
    }

    public void gameOver(Graphics g) {
        g.setColor(Color.red);
        g.setFont(new Font("Ink Free", Font.BOLD, 75));
        FontMetrics metrics = getFontMetrics(g.getFont());
        g.drawString("Game Over", (SCREEN_WIDTH - metrics.stringWidth("Game Over")) / 2, SCREEN_HEIGHT / 2);
        g.setFont(new Font("Ink Free", Font.BOLD, 40));
        g.drawString("Score: " + applesEaten, (SCREEN_WIDTH - metrics.stringWidth("Score: " + applesEaten)) / 2, SCREEN_HEIGHT / 2 + g.getFont().getSize());

        showButtons();
    }

    private void showButtons() {
        restartButton.setVisible(true);
        exitButton.setVisible(true);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (running) {
            move();
            checkApple();
            checkCollisions();
        }
        repaint();
    }

    public class MyKeyAdapter extends KeyAdapter {
        @Override
        public void keyPressed(KeyEvent e) {
            switch (e.getKeyCode()) {
                case KeyEvent.VK_LEFT:
                    if (direction != 'R') {
                        direction = 'L';
                    }
                    break;
                case KeyEvent.VK_RIGHT:
                    if (direction != 'L') {
                        direction = 'R';
                    }
                    break;
                case KeyEvent.VK_UP:
                    if (direction != 'D') {
                        direction = 'U';
                    }
                    break;
                case KeyEvent.VK_DOWN:
                    if (direction != 'U') {
                        direction = 'D';
                    }
                    break;
            }
        }
    }
}
