import re

# tokenizer
def tokenize(expr):
    tokens = re.findall(r'\\|λ|\(|\)|\w+|\.', expr) 
    return [t for t in tokens if t.strip()]  # Remove spaces

# classes for lambda calculus
class Var:
    def __init__(self, name):
        self.name = name
    def __repr__(self):
        return self.name

class Abs:
    def __init__(self, param, body):
        self.param = param
        self.body = body
    def __repr__(self):
        return f"(λ{self.param}.{self.body})"

class App:
    def __init__(self, func, arg):
        self.func = func
        self.arg = arg
    def __repr__(self):
        return f"({self.func} {self.arg})"

# Parser for converting tokens
def parse(tokens):
    def parse_expression(index):
        if index >= len(tokens):
            raise SyntaxError("unexpected end")

        token = tokens[index]

        if token.isalnum():  # var
            return Var(token), index + 1

        elif token in ["\\", "λ"]:  # Abstraction (λx. M)
            if index + 2 >= len(tokens) or tokens[index + 2] != ".":
                raise SyntaxError("should be '.' after lambda parameter")
            param = tokens[index + 1]
            body, next_index = parse_expression(index + 3)
            return Abs(param, body), next_index

        elif token == "(":  # application
            exprs = []
            index += 1 
            while index < len(tokens) and tokens[index] != ")":
                expr, index = parse_expression(index)
                exprs.append(expr)
            if index >= len(tokens) or tokens[index] != ")":
                raise SyntaxError("should be closing )")
            index += 1  # moving past )
            result = exprs[0]
            for expr in exprs[1:]: 
                result = App(result, expr)
            return result, index

        raise SyntaxError(f"Unexpected token: {token}")

    expr, index = parse_expression(0)

    while index < len(tokens):  
        expr2, index = parse_expression(index)
        expr = App(expr, expr2)

    return expr


# substitution function
def substitute(expr, var, value):
    if isinstance(expr, Var):
        return value if expr.name == var else expr
    elif isinstance(expr, Abs):
        if expr.param == var:
            return expr  
        return Abs(expr.param, substitute(expr.body, var, value))
    elif isinstance(expr, App):
        return App(substitute(expr.func, var, value), substitute(expr.arg, var, value))

# beta reduction function to fully reduce expression
def beta_reduce(expr):
    while True:
        reduced = beta_reduce_step(expr)
        if reduced == expr:  # stopping for no more changes
            return expr
        expr = reduced

def beta_reduce_step(expr):
    if isinstance(expr, App):
        if isinstance(expr.func, Abs):  
            return substitute(expr.func.body, expr.func.param, expr.arg)  # apply function
        else:
            return App(beta_reduce_step(expr.func), beta_reduce_step(expr.arg))  # reducing both sides
    elif isinstance(expr, Abs):
        return Abs(expr.param, beta_reduce_step(expr.body))
    return expr

# complete interpreter
def interpret(expr):
    tokens = tokenize(expr)
   # print("Tokens:", tokens) debug
    parsed = parse(tokens)
    # print("Parsed:", parsed) debug
    result = beta_reduce(parsed)
    return result

def repl():
    print("Lambda calculus interpreter")
    print("'exit' to quit.")
    
    while True:
        try:
            expression = input("> ")
            # print(f"Input expression: {expression}") 

            if expression.lower() == "exit":
                break  # exit the loop if 'exit' is typed

            # interpret and reduce the expression
            result = interpret(expression)
            print("Result:", result) 
        except Exception as e:
            print("Error:", e) 
          #  print("Expression failed:", expression)  debug

if __name__ == "__main__":
    repl()
