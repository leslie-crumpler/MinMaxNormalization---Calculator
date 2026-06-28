import json

def lambda_handler(event, context):
    """
    Min-Max Normalization Lambda Function

    Scales a value to the range [0, 1] using a minimum and maximum value.

    Formula:
        normalized = (x - x_min) / (x_max - x_min)
    """

    try:
        # Retrieve input values from the Lambda event
        x = float(event['x'])
        x_min = float(event['x_min'])
        x_max = float(event['x_max'])

    # Handle missing input parameters
    except KeyError as e:
        return {
            'statusCode': 400,
            'body': json.dumps({
                'error': f'Missing required parameter: {str(e)}'
            })
        }

    # Handle invalid numeric values
    except (ValueError, TypeError) as e:
        return {
            'statusCode': 400,
            'body': json.dumps({
                'error': f'Invalid parameter type: {str(e)}'
            })
        }

    # Prevent division by zero when minimum and maximum are equal
    if x_max == x_min:
        return {
            'statusCode': 400,
            'body': json.dumps({
                'error': 'x_max and x_min must not be equal (division by zero).'
            })
        }

    # Calculate the normalized value
    normalized = (x - x_min) / (x_max - x_min)

    # Return the results in JSON format
    return {
        'statusCode': 200,
        'body': json.dumps({
            'function': 'Min-Max Normalization',
            'input': {
                'x': x,
                'x_min': x_min,
                'x_max': x_max
            },
            'normalized': round(normalized, 6),
            'note': 'Result is in range [0, 1] when x is within [x_min, x_max]'
        })
    }


# Local testing section
# This code runs only when executing the script directly
if __name__ == '__main__':

    # Three test cases used to validate the function
    test_cases = [
        {'x': 50, 'x_min': 0, 'x_max': 100},
        {'x': 25, 'x_min': 10, 'x_max': 90},
        {'x': 200, 'x_min': 150, 'x_max': 250}
    ]

    # Execute each test case and display the results
    for i, tc in enumerate(test_cases, 1):
        result = lambda_handler(tc, None)
        body = json.loads(result['body'])

        print(f"Test {i}: {tc}")
        print(f"  Status : {result['statusCode']}")
        print(f"  Result : {body}\n")

