
*Linear Regression* is a simple [Regression](Regression.md) algorithm for modeling the linear relationship between one or more variables.

![](../Attachments/Pasted%20image%2020230225170439.png)


## Implementation

1. A fit line is placed on the mean value of the y axis, with a slope of zero.
2. Calculate the sum of squared residuals (distance from all points to the line).
3. Change the slope and intercept and calculate the squared residuals again. 
4. Keep repeating Step 3 until you reach the "least squares" value, the minimum value of squared residuals. This will be where the derivative of the function is zero, as there is only one global minimum with linear regression.

![](../Attachments/Pasted%20image%2020230225171830.png)

Linear Regression quantifies the linear relationship in the data (This is also the [Metric](ML%20Metrics.md) $R^2$). We want $R^2$ to be as large as possible. Linear Regression also determines how reliable that relationship can be (which is the p-value that can be calculated by the number of points). This gives us a confidence band, thus we want the p-value to be as small as possible.