# 🐍 Classic Snake Game (Java)

A visually appealing Snake Game built in Java Swing, with real-time controls and potential for exciting future upgrades.

## 🎮 Game Overview

### 🏁 Objective
Control a snake to eat food and grow. The game ends if the snake hits itself or the screen edges.

## 🧱 Setup
Grid size: 600x600 pixels
Snake starts at center, facing right
Randomly spawning food

## 🎮 Gameplay

### 🐍 Movement
- Controlled by arrow keys (↑ ↓ ← →)
- Moves continuously in the last chosen direction
- No edge wrapping — hitting edges ends the game

### 🍎 Eating Food
- Dark blue circles appear as food
- Snake grows when it eats food

### 📈 Scoring
- Score increases with each food item eaten
- Speed may increase in future updates

### ❌ Game Over
- Occurs if:
  - Snake collides with itself
  - Snake hits the boundary

---

## ✨ Features

- Snake: light yellow (`#FFFF99`)
- Food: dark blue (`#00008B`)
- Smooth real-time control
- Continuous movement animation

---

## 🔮 Future Enhancements

- Dynamic difficulty
- Obstacles/hazards
- Game levels
- Sound effects
- High score system

---

## 🛠 Installation

### Requirements
- Java JDK 8+
- Java IDE (e.g., IntelliJ, Eclipse, VS Code) or terminal with `javac`/`java`

# Snake-Game
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.Random;

public class SnakeGame extends JFrame {

    public SnakeGame() {
        setTitle("Classic Snake Game");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setResizable(false);
        add(new GamePanel());
        pack();
        setLocationRelativeTo(null);
        setVisible(true);
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(SnakeGame::new);
    }
}

class GamePanel extends JPanel implements ActionListener {

    private final int TILE_SIZE = 25;
    private final int WIDTH = 600;
    private final int HEIGHT = 600;
    private final int ALL_TILES = (WIDTH * HEIGHT) / (TILE_SIZE * TILE_SIZE);

    private final int x[] = new int[ALL_TILES];
    private final int y[] = new int[ALL_TILES];

    private int snakeLength;
    private int foodX;
    private int foodY;

    private char direction = 'R';
    private boolean running = false;

    private Timer timer;
    private final int DELAY = 100; // Snake speed (lower is faster)
    private Random random;

    public GamePanel() {
        setPreferredSize(new Dimension(WIDTH, HEIGHT));
        setBackground(Color.black);
        setFocusable(true);
        addKeyListener(new MyKeyAdapter());
        startGame();
    }

    public void startGame() {
        snakeLength = 6;
        direction = 'R';
        random = new Random();
        spawnFood();
        running = true;
        timer = new Timer(DELAY, this);
        timer.start();
    }

    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        draw(g);
    }

    public void draw(Graphics g) {
        if (running) {
            // Draw grid
            g.setColor(Color.darkGray);
            for (int i = 0; i < HEIGHT / TILE_SIZE; i++) {
                g.drawLine(i * TILE_SIZE, 0, i * TILE_SIZE, HEIGHT);
                g.drawLine(0, i * TILE_SIZE, WIDTH, i * TILE_SIZE);
            }

            // Draw food
            g.setColor(Color.red);
            g.fillOval(foodX, foodY, TILE_SIZE, TILE_SIZE);

            // Draw snake
            for (int i = 0; i < snakeLength; i++) {
                if (i == 0) {
                    g.setColor(Color.green);
                } else {
                    g.setColor(new Color(45, 180, 0));
                }
                g.fillRect(x[i], y[i], TILE_SIZE, TILE_SIZE);
            }

            // Score
            g.setColor(Color.white);
            g.setFont(new Font("Arial", Font.BOLD, 20));
            g.drawString("Score: " + (snakeLength - 6), 10, 20);

        } else {
            gameOver(g);
        }
    }

    public void spawnFood() {
        foodX = random.nextInt((WIDTH / TILE_SIZE)) * TILE_SIZE;
        foodY = random.nextInt((HEIGHT / TILE_SIZE)) * TILE_SIZE;
    }

    public void move() {
        for (int i = snakeLength; i > 0; i--) {
            x[i] = x[i - 1];
            y[i] = y[i - 1];
        }

        switch (direction) {
            case 'U' -> y[0] -= TILE_SIZE;
            case 'D' -> y[0] += TILE_SIZE;
            case 'L' -> x[0] -= TILE_SIZE;
            case 'R' -> x[0] += TILE_SIZE;
        }
    }

    public void checkFood() {
        if (x[0] == foodX && y[0] == foodY) {
            snakeLength++;
            spawnFood();
        }
    }

    public void checkCollisions() {
        // Check body collision
        for (int i = snakeLength; i > 0; i--) {
            if (x[0] == x[i] && y[0] == y[i]) {
                running = false;
                break;
            }
        }

        // Wall collision
        if (x[0] < 0 || x[0] >= WIDTH || y[0] < 0 || y[0] >= HEIGHT) {
            running = false;
        }

        if (!running) {
            timer.stop();
        }
    }

    public void gameOver(Graphics g) {
        g.setColor(Color.red);
        g.setFont(new Font("Arial", Font.BOLD, 40));
        FontMetrics metrics = getFontMetrics(g.getFont());
        g.drawString("Game Over", (WIDTH - metrics.stringWidth("Game Over")) / 2, HEIGHT / 2);

        g.setColor(Color.white);
        g.setFont(new Font("Arial", Font.PLAIN, 20));
        g.drawString("Final Score: " + (snakeLength - 6), WIDTH / 2 - 60, HEIGHT / 2 + 30);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (running) {
            move();
            checkFood();
            checkCollisions();
        }
        repaint();
    }

    private class MyKeyAdapter extends KeyAdapter {
        @Override
        public void keyPressed(KeyEvent e) {
            switch (e.getKeyCode()) {
                case KeyEvent.VK_LEFT:
                    if (direction != 'R') direction = 'L';
                    break;
                case KeyEvent.VK_RIGHT:
                    if (direction != 'L') direction = 'R';
                    break;
                case KeyEvent.VK_UP:
                    if (direction != 'D') direction = 'U';
                    break;
                case KeyEvent.VK_DOWN:
                    if (direction != 'U') direction = 'D';
                    break;
            }
        }
    }
}
