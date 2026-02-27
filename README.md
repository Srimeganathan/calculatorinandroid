### Ex.No:5 Develop a program to create a simple calculator using android studio.
# AIM:
To Develop a program to create a simple calculator using android studio.

# EQUIPMENTS REQUIRED:
Android Studio(Min. required Artic Fox)

# ALGORITHM:
Initialize Project: Create a new Android Studio project using the Empty Views Activity template.

Design Layout: Finish activity_main.xml ensuring unique IDs for your two inputs, result display, and four operator buttons.

Link UI Elements: In MainActivity.java, use findViewById() to connect all XML elements to corresponding Java variables.

Create Logic Method: Define a single Java function (e.g., calculateAndDisplay) that takes the required operator as input.

Attach Listeners: Set up OnClickListener for all four buttons, each calling the logic method with its specific operator character.

Perform Calculation: Inside the logic method, convert inputs to numbers and use a switch statement to execute the correct math (and check for divide-by-zero).

7.Display Output: Update the tvResult TextView with the final answer or show an appropriate error message using Toast.

# PROGRAM:
Activity.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:background="#121212"
    android:padding="20dp">

    <!-- Display -->
    <TextView
        android:id="@+id/display"
        android:layout_width="match_parent"
        android:layout_height="120dp"
        android:background="#1E1E1E"
        android:text="0"
        android:textSize="40sp"
        android:textStyle="bold"
        android:textColor="#FFFFFF"
        android:gravity="end|center_vertical"
        android:padding="20dp"
        android:layout_marginBottom="20dp"
        android:elevation="6dp"/>

    <!-- Buttons -->
    <GridLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:columnCount="4">

        <!-- Row 1 -->
        <Button
            android:id="@+id/btnC"
            style="@style/operatorButton"
            android:text="C"/>

        <Button
            android:id="@+id/btnBack"
            style="@style/operatorButton"
            android:text="⌫"/>

        <Button
            android:id="@+id/btnPercent"
            style="@style/operatorButton"
            android:text="%"/>

        <Button
            android:id="@+id/btnDiv"
            style="@style/operatorButton"
            android:text="÷"/>

        <!-- Row 2 -->
        <Button android:id="@+id/btn7" style="@style/numberButton" android:text="7"/>
        <Button android:id="@+id/btn8" style="@style/numberButton" android:text="8"/>
        <Button android:id="@+id/btn9" style="@style/numberButton" android:text="9"/>
        <Button android:id="@+id/btnMul" style="@style/operatorButton" android:text="×"/>

        <!-- Row 3 -->
        <Button android:id="@+id/btn4" style="@style/numberButton" android:text="4"/>
        <Button android:id="@+id/btn5" style="@style/numberButton" android:text="5"/>
        <Button android:id="@+id/btn6" style="@style/numberButton" android:text="6"/>
        <Button android:id="@+id/btnSub" style="@style/operatorButton" android:text="-"/>

        <!-- Row 4 -->
        <Button android:id="@+id/btn1" style="@style/numberButton" android:text="1"/>
        <Button android:id="@+id/btn2" style="@style/numberButton" android:text="2"/>
        <Button android:id="@+id/btn3" style="@style/numberButton" android:text="3"/>
        <Button android:id="@+id/btnAdd" style="@style/operatorButton" android:text="+"/>

        <!-- Row 5 -->
        <Button
            android:id="@+id/btn0"
            style="@style/numberButton"
            android:text="0"
            android:layout_columnSpan="2"/>

        <Button
            android:id="@+id/btnDot"
            style="@style/numberButton"
            android:text="."/>

        <Button
            android:id="@+id/btnEqual"
            style="@style/equalButton"
            android:text="="/>

    </GridLayout>

</LinearLayout>
```
MainActivity.java
```
package com.example.calculator;

import androidx.appcompat.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import java.util.Stack;

public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    TextView display;
    String input = "";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        display = findViewById(R.id.display);

        int[] buttonIds = {
                R.id.btn0,R.id.btn1,R.id.btn2,R.id.btn3,R.id.btn4,R.id.btn5,R.id.btn6,R.id.btn7,R.id.btn8,R.id.btn9,
                R.id.btnAdd,R.id.btnSub,R.id.btnMul,R.id.btnDiv,R.id.btnDot,R.id.btnEqual,R.id.btnC,R.id.btnBack,R.id.btnPercent
        };

        for (int id : buttonIds) {
            findViewById(id).setOnClickListener(this);
        }
    }

    @Override
    public void onClick(View v) {
        Button b = (Button) v;
        String buttonText = b.getText().toString();

        switch (buttonText) {
            case "C":
                input = "";
                break;
            case "⌫":
                if (!input.isEmpty())
                    input = input.substring(0, input.length() - 1);
                break;
            case "=":
                try {
                    double result = evaluate(input);
                    if(result == (long)result)
                        input = String.valueOf((long)result);
                    else
                        input = String.valueOf(result);
                } catch (Exception e) {
                    input = "Error";
                }
                break;
            default:
                input += buttonText;
        }
        display.setText(input.isEmpty() ? "0" : input);
    }

    // Simple evaluation using stacks for +, -, *, /, %
    private double evaluate(String expr) throws Exception {
        expr = expr.replace("×", "*").replace("÷", "/").replace("%", "/100");
        return evalExpression(expr);
    }

    private double evalExpression(String expr) throws Exception {
        // Remove spaces
        expr = expr.replaceAll(" ", "");
        char[] tokens = expr.toCharArray();

        Stack<Double> values = new Stack<>();
        Stack<Character> ops = new Stack<>();
        for (int i = 0; i < tokens.length; i++) {
            if (tokens[i] >= '0' && tokens[i] <= '9' || tokens[i]=='.') {
                StringBuilder sbuf = new StringBuilder();
                while (i < tokens.length && (tokens[i] >= '0' && tokens[i] <= '9' || tokens[i]=='.'))
                    sbuf.append(tokens[i++]);
                i--;
                values.push(Double.parseDouble(sbuf.toString()));
            } else if (tokens[i] == '+' || tokens[i] == '-' || tokens[i] == '*' || tokens[i] == '/') {
                while (!ops.empty() && hasPrecedence(tokens[i], ops.peek()))
                    values.push(applyOp(ops.pop(), values.pop(), values.pop()));
                ops.push(tokens[i]);
            }
        }
        while (!ops.empty())
            values.push(applyOp(ops.pop(), values.pop(), values.pop()));

        return values.pop();
    }

    private boolean hasPrecedence(char op1, char op2) {
        if ((op2 == '*' || op2 == '/') && (op1 == '+' || op1 == '-'))
            return true;
        else
            return (op2 != '(' && op2 != ')');
    }

    private double applyOp(char op, double b, double a) throws Exception {
        switch (op) {
            case '+': return a + b;
            case '-': return a - b;
            case '*': return a * b;
            case '/': if (b == 0) throw new Exception("Division by zero"); return a / b;
        }
        return 0;
    }
}
```
Styles.xml
```
<resources>

    <!-- Number Buttons -->
    <style name="numberButton">
        <item name="android:layout_width">0dp</item>
        <item name="android:layout_height">80dp</item>
        <item name="android:layout_margin">8dp</item>
        <item name="android:layout_columnWeight">1</item>
        <item name="android:backgroundTint">#2C2C2C</item>
        <item name="android:textColor">#FFFFFF</item>
        <item name="android:textSize">22sp</item>
    </style>

    <!-- Operator Buttons -->
    <style name="operatorButton">
        <item name="android:layout_width">0dp</item>
        <item name="android:layout_height">80dp</item>
        <item name="android:layout_margin">8dp</item>
        <item name="android:layout_columnWeight">1</item>
        <item name="android:backgroundTint">#FF9800</item>
        <item name="android:textColor">#FFFFFF</item>
        <item name="android:textSize">22sp</item>
    </style>

    <!-- Equal Button -->
    <style name="equalButton">
        <item name="android:layout_width">0dp</item>
        <item name="android:layout_height">80dp</item>
        <item name="android:layout_margin">8dp</item>
        <item name="android:layout_columnWeight">1</item>
        <item name="android:backgroundTint">#00C853</item>
        <item name="android:textColor">#FFFFFF</item>
        <item name="android:textSize">24sp</item>
        <item name="android:textStyle">bold</item>
    </style>

</resources>
```
# OUTPUT

<img width="239" height="533" alt="image" src="https://github.com/user-attachments/assets/b8304f05-961b-4f63-a5fd-1edf52e63761" />

# RESULT
Thus a Simple Android Application to create your own simple calculator using Android Studio is developed and executed successfully.
