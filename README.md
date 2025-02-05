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
