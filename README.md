import operator
import uuid
import math

class Stack:
    def __init__(self):
        self.items = []

    def is_empty(self):
        return not bool(self.items)

    def push(self, value):
        self.items.append(value)

    def pop(self):
        if not self.is_empty():
            return self.items.pop()
        else:
            print("Stack is empty")
            return None

    def peek(self):
        if not self.is_empty():
            return self.items[-1]

    def size(self):
        return len(self.items)

class Calculator:
    def __init__(self):
        self.id = uuid.uuid4()
        self.stack = Stack()
        self.operators = {'+': operator.add, '-': operator.sub, '*': operator.mul, '/': operator.truediv}

    def calculate(self, expression):
        operand = ''
        for char in expression:
            if char.isdigit():
                operand += char
            elif char in self.operators.keys():
                if operand:
                    self.stack.push(int(operand))
                    operand = ''
                self.stack.push(char)
            else:
                print(f"Unknown token: {char}")
        
        if operand:
            self.stack.push(int(operand))

        while len(self.stack.items) > 1:
            operand2 = self.stack.pop()
            operator = self.stack.pop()
            operand1 = self.stack.pop()

            if operand1 is None or operand2 is None or operator not in self.operators:
                print("Insufficient operands or invalid operator")
                return None

            result = self.operators[operator](operand1, operand2)
            self.stack.push(result)

        return self.stack.pop()

    def __del__(self):
        print(f"Calculator {self.id} is being deleted")

class EngineerCalculator(Calculator):
    def __init__(self):
        super().__init__()
        self.constants = {'pi': math.pi, 'e': math.e}
        self.operators.update({'sin': math.sin, 'cos': math.cos, 'tan': math.tan})

    def factorial(self, n):
        if n == 0:
            return 1
        else:
            return n * self.factorial(n-1)

    def calculate(self, expression):
        tokens = expression.split()
        for token in tokens:
            if token in self.constants.keys():
                self.stack.push(self.constants[token])
            elif token in self.operators.keys():
                operand2 = self.stack.pop()
                operand1 = self.stack.pop()

                if operand1 is None or operand2 is None:
                    print("Insufficient operands for operation")
                    return None

                result = self.operators[token](operand1, operand2)
                self.stack.push(result)
            elif token.isdigit():
                self.stack.push(int(token))
            else:
                print(f"Unknown token: {token}")
        return self.stack.pop()

    def __del__(self):
        print(f"EngineerCalculator {self.id} is being deleted")

if __name__ == "__main__":
    calc = Calculator()
    eng_calc = EngineerCalculator()

    while True:
        print("1. 기본 계산기 사용")
        print("2. 공학용 계산기 사용")
        print("3. 종료")
        choice = input("선택: ")

        if choice == "1":
            expression = input("수식 입력: ")
            result = calc.calculate(expression)
            if result is not None:
                print("결과: ", result)
        elif choice == "2":
            expression = input("수식 입력: ")
            result = eng_calc.calculate(expression)
            if result is not None:
                print("결과: ", result)
        elif choice == "3":
            del calc
            del eng_calc
            break
        else:
            print("잘못된 선택입니다.")
