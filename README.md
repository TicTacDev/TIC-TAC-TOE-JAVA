import javax.swing.*;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;

public class TicTacToe {
    private JFrame frame;
    private JPanel panel;
    private int[][] markers;
    private int player;
    private boolean gameOver;
    private int winner;
    private String state;

    private final int CELL_SIZE = 100;
    private final int GRID_SIZE = 3;

    public TicTacToe() {
        markers = new int[GRID_SIZE][GRID_SIZE];
        player = 1;
        gameOver = false;
        winner = 0;
        state = "menu"; // Menu or game

        // Initialize the GUI
        frame = new JFrame("Tic Tac Toe");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(320, 340);
        frame.setResizable(false);

        panel = new JPanel() {
            @Override
            protected void paintComponent(Graphics g) {
                super.paintComponent(g);
                if (state.equals("menu")) {
                    drawMenu(g);
                } else if (state.equals("game")) {
                    drawBoard(g);
                    drawMarkers(g);
                    if (gameOver) {
                        drawGameOver(g);
                    }
                }
            }
        };
        panel.setBackground(new Color(255, 255, 210));
        panel.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseReleased(MouseEvent e) {
                handleClick(e.getX(), e.getY());
            }
        });

        frame.add(panel);
        frame.setVisible(true);
    }

    private void drawMenu(Graphics g) {
        g.setFont(new Font("SansSerif", Font.BOLD, 30));
        g.setColor(new Color(0, 0, 255));
        g.drawString("Tic Tac Toe", 70, 100);

        g.setColor(new Color(0, 255, 0));
        g.fillRect(70, 150, 160, 50);

        g.setColor(new Color(0, 0, 255));
        g.setFont(new Font("SansSerif", Font.PLAIN, 20));
        g.drawString("Start Game", 90, 180);
    }

    private void drawBoard(Graphics g) {
        g.setColor(new Color(50, 50, 50));
        for (int i = 1; i < GRID_SIZE; i++) {
            g.fillRect(i * CELL_SIZE - 3, 0, 6, CELL_SIZE * GRID_SIZE);
            g.fillRect(0, i * CELL_SIZE - 3, CELL_SIZE * GRID_SIZE, 6);
        }
    }

    private void drawMarkers(Graphics g) {
        for (int x = 0; x < GRID_SIZE; x++) {
            for (int y = 0; y < GRID_SIZE; y++) {
                if (markers[x][y] == 1) {
                    g.setColor(Color.RED);
                    g.drawLine(x * CELL_SIZE + 15, y * CELL_SIZE + 15, (x + 1) * CELL_SIZE - 15, (y + 1) * CELL_SIZE - 15);
                    g.drawLine((x + 1) * CELL_SIZE - 15, y * CELL_SIZE + 15, x * CELL_SIZE + 15, (y + 1) * CELL_SIZE - 15);
                } else if (markers[x][y] == -1) {
                    g.setColor(Color.GREEN);
                    g.drawOval(x * CELL_SIZE + 15, y * CELL_SIZE + 15, CELL_SIZE - 30, CELL_SIZE - 30);
                }
            }
        }
    }

    private void drawGameOver(Graphics g) {
        g.setColor(new Color(0, 255, 0));
        g.fillRect(50, 100, 200, 100);

        g.setColor(new Color(0, 0, 255));
        g.setFont(new Font("SansSerif", Font.BOLD, 20));
        String message = winner == 0 ? "It's a Tie!" : "Player " + winner + " Wins!";
        g.drawString(message, 80, 130);

        g.setColor(new Color(0, 255, 0));
        g.fillRect(80, 180, 160, 40);

        g.setColor(new Color(0, 0, 255));
        g.setFont(new Font("SansSerif", Font.PLAIN, 16));
        g.drawString("Play Again", 110, 205);
    }

    private void handleClick(int x, int y) {
        if (state.equals("menu")) {
            if (x >= 70 && x <= 230 && y >= 150 && y <= 200) {
                state = "game";
                panel.repaint();
            }
        } else if (state.equals("game")) {
            if (!gameOver) {
                int cellX = x / CELL_SIZE;
                int cellY = y / CELL_SIZE;
                if (cellX < GRID_SIZE && cellY < GRID_SIZE && markers[cellX][cellY] == 0) {
                    markers[cellX][cellY] = player;
                    player *= -1;
                    checkGameOver();
                }
            } else {
                if (x >= 80 && x <= 240 && y >= 180 && y <= 220) {
                    resetGame();
                }
            }
            panel.repaint();
        }
    }

    private void checkGameOver() {
        for (int i = 0; i < GRID_SIZE; i++) {
            if (Math.abs(markers[i][0] + markers[i][1] + markers[i][2]) == 3) {
                winner = markers[i][0];
                gameOver = true;
                return;
            }
            if (Math.abs(markers[0][i] + markers[1][i] + markers[2][i]) == 3) {
                winner = markers[0][i];
                gameOver = true;
                return;
            }
        }
        if (Math.abs(markers[0][0] + markers[1][1] + markers[2][2]) == 3 || Math.abs(markers[2][0] + markers[1][1] + markers[0][2]) == 3) {
            winner = markers[1][1];
            gameOver = true;
            return;
        }

        boolean tie = true;
        for (int[] row : markers) {
            for (int cell : row) {
                if (cell == 0) {
                    tie = false;
                    break;
                }
            }
        }
        if (tie) {
            winner = 0;
            gameOver = true;
        }
    }

    private void resetGame() {
        markers = new int[GRID_SIZE][GRID_SIZE];
        player = 1;
        gameOver = false;
        winner = 0;
    }

    public static void main(String[] args) {
        new TicTacToe();
    }
}
