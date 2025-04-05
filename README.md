# NUMBER_CALCULATOR

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.script.ScriptEngine;
import javax.script.ScriptEngineManager;


public class Calculator {
    private JFrame frame;
    private JTextField display;
    private StringBuilder currentExpression;
    private String totalExpression;

    public Calculator() {
        frame = new JFrame("Calculator");
        frame.setSize(375, 667);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLayout(new BorderLayout());

        currentExpression = new StringBuilder();
        totalExpression = "";

        createDisplayPanel();
        createButtonsPanel();

        frame.setVisible(true);
    }

    private void createDisplayPanel() {
        JPanel displayPanel = new JPanel();
        displayPanel.setLayout(new BorderLayout());

        display = new JTextField();
        display.setFont(new Font("Arial", Font.BOLD, 40));
        display.setHorizontalAlignment(SwingConstants.RIGHT);
        display.setEditable(false);
        display.setBackground(new Color(245, 245, 245));

        displayPanel.add(display, BorderLayout.CENTER);
        frame.add(displayPanel, BorderLayout.NORTH);
    }

    private void createButtonsPanel() {
        JPanel buttonsPanel = new JPanel();
        buttonsPanel.setLayout(new GridLayout(5, 4, 5, 5));

        String[] buttons = {
                "C", "\u221Ax", "x^2", "/",
                "7", "8", "9", "*",
                "4", "5", "6", "-",
                "1", "2", "3", "+",
                ".", "0", "=", ""
        };

        for (String button : buttons) {
            JButton btn = new JButton(button);
            btn.setFont(new Font("Arial", Font.PLAIN, 20));

            btn.addActionListener(new ActionListener() {
                @Override
                public void actionPerformed(ActionEvent e) {
                    handleButtonClick(button);
                }
            });
            buttonsPanel.add(btn);
        }

        frame.add(buttonsPanel, BorderLayout.CENTER);
    }

    private void handleButtonClick(String button) {
        switch (button) {
            case "C":
                clear();
                break;
            case "=":
                evaluate();
                break;
            case "x^2":
                square();
                break;
            case "\u221Ax":
                squareRoot();
                break;
            case "+":
            case "-":
            case "*":
            case "/":
                appendOperator(button);
                break;
            default:
                appendDigit(button);
        }
    }

    private void clear() {
        currentExpression.setLength(0);
        totalExpression = "";
        updateDisplay();
    }

    private void evaluate() {
        totalExpression += currentExpression.toString();
        try {
            double result = eval(totalExpression);
            currentExpression.setLength(0);
            currentExpression.append(result);
            totalExpression = "";
        } catch (Exception e) {
            currentExpression.setLength(0);
            currentExpression.append("Error");
        }
        updateDisplay();
    }

    private void square() {
        try {
            double value = Double.parseDouble(currentExpression.toString());
            currentExpression.setLength(0);
            currentExpression.append(value * value);
            updateDisplay();
        } catch (NumberFormatException e) {
            currentExpression.setLength(0);
            currentExpression.append("Error");
        }
    }

    private void squareRoot() {
        try {
            double value = Double.parseDouble(currentExpression.toString());
            currentExpression.setLength(0);
            currentExpression.append(Math.sqrt(value));
            updateDisplay();
        } catch (NumberFormatException e) {
            currentExpression.setLength(0);
            currentExpression.append("Error");
        }
    }

    private void appendOperator(String operator) {
        totalExpression += currentExpression.toString() + operator;
        currentExpression.setLength(0);
        updateDisplay();
    }

    private void appendDigit(String digit) {
        currentExpression.append(digit);
        updateDisplay();
    }

    private void updateDisplay() {
        display.setText(currentExpression.toString());
    }

    private double eval(String expression) throws Exception {
        ScriptEngineManager manager = new ScriptEngineManager();
        ScriptEngine engine = manager.getEngineByName("JavaScript");
        return Double.parseDouble(engine.eval(expression).toString());
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(Calculator::new);
    }
}
