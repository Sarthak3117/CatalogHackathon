Shamir's Secret Sharing Simplified
This repository contains an implementation of a simplified version of Shamir's Secret Sharing algorithm. The program uses Lagrange interpolation to recover the constant term (secret) from a set of points derived from a JSON object containing values encoded in different numerical bases.

How it works
The script parses a set of key-value pairs where each value represents a point on a polynomial, and then it applies Lagrange interpolation to calculate the constant term (which can be thought of as the "secret"). The JSON object provides information about the number of required points (k) and the total number of points (n) needed to reconstruct the secret.

Key Features
Lagrange Interpolation: This is used to find the constant term of a polynomial, which represents the secret.
Base Conversion: The script supports points encoded in various number bases (e.g., binary, decimal, or other custom bases).
Error Handling: It checks if there are enough points to perform interpolation based on the value of k.
Code Explanation
Step 1: Base Conversion Function
javascript
Copy code
const baseToDecimal = (value, base) => parseInt(value, base);
This function converts a string value from its given base (e.g., binary, decimal) into a decimal number.

Step 2: Lagrange Interpolation Function
javascript
Copy code
const lagrangeInterpolation = (points) => {
    const n = points.length;
    let c = 0;

    for (let i = 0; i < n; i++) {
        let xi = points[i][0];
        let yi = points[i][1];
        let term = yi;

        for (let j = 0; j < n; j++) {
            if (i !== j) {
                let xj = points[j][0];
                term *= (0 - xj) / (xi - xj);
            }
        }
        c += term;
    }

    return c;
};
This function takes an array of points and performs Lagrange interpolation to find the constant term of the polynomial. The points array contains pairs of x and y values. The formula used is based on the Lagrange polynomial method.

Step 3: Parse JSON Input
javascript
Copy code
const jsonString = `{
    "keys": {
        "n": 4,
        "k": 3
    },
    "1": {
        "base": "10",
        "value": "4"
    },
    "2": {
        "base": "2",
        "value": "111"
    },
    "3": {
        "base": "10",
        "value": "12"
    },
    "6": {
        "base": "4",
        "value": "213"
    }
}`;
The jsonString contains the number of points n, the threshold number of points k, and various points (in different number bases).

Step 4: Convert JSON to Object
javascript
Copy code
const jsonObject = JSON.parse(jsonString);
This converts the JSON string into a JavaScript object for further processing.

Step 5: Extract Points
javascript
Copy code
const points = [];
for (const [key, value] of Object.entries(jsonObject)) {
    if (key === 'keys') continue;

    const base = parseInt(value.base);
    const decodedValue = baseToDecimal(value.value, base);
    const x = parseInt(key);
    points.push([x, decodedValue]);
}
This part loops through the entries in the JSON object, converts the value field from its base into a decimal number using the baseToDecimal function, and stores the resulting points as [x, decodedValue] pairs.

Step 6: Check for Enough Points
javascript
Copy code
if (points.length < k) {
    console.error("Not enough points to perform interpolation.");
    process.exit(1);
}
If the number of points available is less than k (the required number of points to reconstruct the secret), an error is shown, and the program stops.

Step 7: Perform Lagrange Interpolation
javascript
Copy code
const secretC = lagrangeInterpolation(points);
console.log(`The constant term (secret c) is: ${secretC}`);
Finally, the Lagrange interpolation is performed using the extracted points, and the constant term (secret) is printed to the console.

Usage
To run this script:

Clone the repository to your local machine.
Ensure Node.js is installed.
Run the script with Node:
bash
Copy code
node ShamirSecretSharing.js
Output
The program will output the constant term (secret) based on the points provided in the JSON input.

Notes
You can modify the jsonString to test different sets of points.
Make sure that the number of points is at least equal to k for successful interpolation.
