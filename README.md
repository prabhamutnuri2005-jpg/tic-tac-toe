import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.Random;

public class TicTacToeAI {

    private JFrame frame;
    private JButton[][] buttons = new JButton[3][3];
    private char human = 'X';
    private char ai = 'O';

    private String difficulty = "Hard";
    private Random rand = new Random();

    public TicTacToeAI() {

        frame = new JFrame("Tic Tac Toe - AI Mode");
        frame.setSize(400, 500);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLayout(new BorderLayout());

        // 🔽 Difficulty Dropdown
        JPanel top = new JPanel();
        JLabel label = new JLabel("Difficulty: ");

        String[] levels = {"Easy", "Medium", "Hard"};
        JComboBox<String> box = new JComboBox<>(levels);

        box.addActionListener(e -> difficulty = box.getSelectedItem().toString());

        top.add(label);
        top.add(box);

        // 🎮 GRID
        JPanel panel = new JPanel();
        panel.setLayout(new GridLayout(3, 3));

        Font font = new Font("Arial", Font.BOLD, 50);

        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                buttons[i][j] = new JButton("");
                buttons[i][j].setFont(font);

                final int r = i;
                final int c = j;

                buttons[i][j].addActionListener(e -> {

                    if (buttons[r][c].getText().equals("") && !isGameOver()) {

                        buttons[r][c].setText("X");
                       buttons[r][c].setForeground(Color.BLUE);

                        if (!isGameOver()) {
                            aiMove();
                        }

                        checkResult();
                    }
                });

                panel.add(buttons[i][j]);
            }
        }

        JButton reset = new JButton("Reset");
        reset.addActionListener(e -> resetGame());

        frame.add(top, BorderLayout.NORTH);
        frame.add(panel, BorderLayout.CENTER);
        frame.add(reset, BorderLayout.SOUTH);

        frame.setVisible(true);
    }

    // 🤖 AI MOVE (Difficulty Based)
    public void aiMove() {

        if (difficulty.equals("Easy")) {
            randomMove();
        }
        else if (difficulty.equals("Medium")) {
            if (rand.nextBoolean()) randomMove();
            else smartMove();
        }
        else {
            smartMove();
        }
    }

    // 🎲 Random move
    public void randomMove() {
        while (true) {
            int r = rand.nextInt(3);
            int c = rand.nextInt(3);

            if (buttons[r][c].getText().equals("")) {
                buttons[r][c].setText("O");
                buttons[r][c].setForeground(Color.red);
                break;
            }
        }
    }

    // 🧠 Smart move
    public void smartMove() {

        if (tryMove('O')) return; // win
        if (tryMove('X')) return; // block

        randomMove();
    }

    public boolean tryMove(char player) {

    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {

            if (buttons[i][j].getText().equals("")) {

                // simulate move
                buttons[i][j].setText(String.valueOf(player));

                boolean win = checkWinner(player);

                // undo move immediately
                buttons[i][j].setText("");

                if (win) {
                    buttons[i][j].setText(String.valueOf(ai)); // actually play move
                    return true;
                }
            }
        }
    }
    return false;
}

    // 🏆 WIN CHECK
    public boolean checkWinner(char player) {

        for (int i = 0; i < 3; i++) {
            if (match(i,0,i,1,i,2,player)) return true;
            if (match(0,i,1,i,2,i,player)) return true;
        }

        return match(0,0,1,1,2,2,player) ||
               match(0,2,1,1,2,0,player);
    }

    public boolean match(int r1,int c1,int r2,int c2,int r3,int c3,char p){
        return buttons[r1][c1].getText().equals(String.valueOf(p)) &&
               buttons[r2][c2].getText().equals(String.valueOf(p)) &&
               buttons[r3][c3].getText().equals(String.valueOf(p));
    }

    // 🎮 GAME STATE
    public boolean isBoardFull() {
        for (int i = 0; i < 3; i++)
            for (int j = 0; j < 3; j++)
                if (buttons[i][j].getText().equals(""))
                    return false;
        return true;
    }

    public boolean isGameOver() {
        return checkWinner(human) || checkWinner(ai) || isBoardFull();
    }

    // 🎯 RESULT
    public void checkResult() {

        if (checkWinner(human)) {
            JOptionPane.showMessageDialog(frame, "🎉 You Win!");
        }
        else if (checkWinner(ai)) {
            JOptionPane.showMessageDialog(frame, "🤖 AI Wins!");
        }
        else if (isBoardFull()) {
            JOptionPane.showMessageDialog(frame, "🤝 Draw!");
        }
    }

    // 🔄 RESET
    public void resetGame() {
        for (int i = 0; i < 3; i++)
            for (int j = 0; j < 3; j++)
                buttons[i][j].setText("");
    }
    

    public static void main(String[] args) {
        new TicTacToeAI();
    }
}
