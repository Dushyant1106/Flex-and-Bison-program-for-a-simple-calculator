# Flex-and-Bison-program-for-a-simple-calculator

.l:
%{
#include <stdio.h>
#include "calc.tab.h"
%}

%%

[0-9]+  { yylval = atoi(yytext); return NUMBER; }
[\t ]+  { /* Ignore whitespace */ }
"\n"    { return NEWLINE; }  // Ensure newline is recognized
"+"     { return PLUS; }
"-"     { return MINUS; }
"*"     { return MULT; }
"/"     { return DIV; }
"("     { return LPAREN; }
")"     { return RPAREN; }

.       { printf("Invalid character: %s\n", yytext); }

%%

int yywrap() {
    return 1;
}
.y:
%{
#include <stdio.h>
#include <stdlib.h>
void yyerror(const char *s);
int yylex();
%}

%token NUMBER PLUS MINUS MULT DIV LPAREN RPAREN NEWLINE
%left PLUS MINUS
%left MULT DIV
%right UMINUS

%%

input:
    | input line
    ;

line:
    expr NEWLINE { printf("Result: %d\n", $1); }
    | NEWLINE  // Allow blank lines without syntax errors
    ;

expr:
    NUMBER            { $$ = $1; }
    | expr PLUS expr  { $$ = $1 + $3; }
    | expr MINUS expr { $$ = $1 - $3; }
    | expr MULT expr  { $$ = $1 * $3; }
    | expr DIV expr   { 
        if ($3 == 0) {
            printf("Error: Division by zero\n");
            $$ = 0;
        } else {
            $$ = $1 / $3;
        }
      }
    | LPAREN expr RPAREN { $$ = $2; }
    | MINUS expr %prec UMINUS { $$ = -$2; }
    ;

%%

void yyerror(const char *s) {
    fprintf(stderr, "Error: %s\n", s);
}

int main() {
    printf("Simple Calculator: Enter expressions (Ctrl+D to exit)\n");
    return yyparse();
}
