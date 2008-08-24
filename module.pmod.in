//! @ignore
constant __author = "Christian Ress <bart@pike.the-bart.org>";
constant __version = "1.0";
constant __components = ({ "Public.pmod/Parser.pmod/JSON3.pmod/module.pmod" });
//! @endignore

#define SMB(x)	#x : (x)

private class myHandler {	
	private mapping(string:function) allowedSymbols = 
		([
			SMB(`-),
			SMB(aggregate_mapping),
	   		SMB(aggregate),
			SMB(allocate)
		]);
	
    mapping(string:mixed)|object get_default_module() {
		return ([ ]);
	}
	
	mixed resolv(string symbol, string filename, object handler) {
		if (zero_type(allowedSymbols[symbol]))
			return UNDEFINED;
		else
			return allowedSymbols[symbol];
	}
}

private int|array convert_to_pike(array|string s) {
	array(string) tokens;
	
	// split string into tokens
	if (stringp(s))
		tokens = Parser.C.split(s);
	else
		tokens = s;
	
	// check if first character is a valid json-token starting character
	int n = sizeof(tokens);
	for(int i = 0; i < n; ++i) {
		switch(tokens[i][0]) {
			case '0':
			case '1':
			case '2':
			case '3':
			case '4':
			case '5':
			case '6':
			case '7':
			case '8':
			case '9':
			case '"':
			case '-':
			case '+':
			case ':':
			case ',':
				break;
#if 0
			case '+':
				tokens[i] = "0" + tokens[i];
				break;
#endif
			case '{': // JSON object, Pike mapping
				tokens[i] = "([";
				break;
			case '}':
				tokens[i] = "])";
				break;
			case '[': // JSON array, Pike array
				tokens[i] = "({";
				break;
			case ']':
				tokens[i] = "})";
				break;
			case ' ':
			case '\t':
			case '\n':
			case '\r':
				tokens[i] = "";
				break;
			default:
				if (tokens[i] == "null") {
					tokens[i] = "UNDEFINED";
					break;
				}
				else if (tokens[i] == "true") {
					tokens[i] = "1";
					break;
				}
				else if (tokens[i] == "false") {
					tokens[i] = "0";
					break;
				}

				return 0;
		}
	}

	return tokens;
}

private int|array convert_to_json(array|string s) {
	array(string) tokens;
	
	// split string into tokens
	if (stringp(s))
		tokens = Parser.C.split(s);
	else
		tokens = s;
	
	// check if first character is a valid pike-token starting character
	int n = sizeof(tokens);
	for(int i = 0; i < n; ++i) {
		switch(tokens[i][0]) {
			case '0':
			case '1':
			case '2':
			case '3':
			case '4':
			case '5':
			case '6':
			case '7':
			case '8':
			case '9':
			case '"':
			case '-':
			case '+':
			case ':':
			case ',':
				break;
			case '{': // JSON object, Pike mapping
				tokens[i] = "[";
				break;
			case '}':
				tokens[i] = "]";
				break;
			case '[': // JSON array, Pike array
				tokens[i] = "{";
				break;
			case ']':
				tokens[i] = "}";
				break;
			case ' ':
			case '\t':
			case '\n':
			case '\r':
			case '/':
			case '(':
			case ')':
				tokens[i] = "";
				break;
			default:
				if (tokens[i] == "null") {
					tokens[i] = "UNDEFINED";
					break;
				}
				else if (tokens[i] == "true") {
					tokens[i] = "1";
					break;
				}
				else if (tokens[i] == "false") {
					tokens[i] = "0";
					break;
				}

				write(">> returning because %O\n", tokens[i]);
				
				return 0;
		}
	}
	
	return tokens;
}

mixed decode(string s) {
	array(string) tokens = Parser.C.split(s);
	
	// check if input is valid
	if ((tokens = convert_to_pike(tokens)) == 0)
		error(sprintf("JSON.parse(%.10s) Invalid JSON serialization.", s));
	
	program x = compile("mixed get() { return " + (tokens*"") + "; }", myHandler());
	object o = x();
	
	return o->get();
}

string encode(mixed var) {
	array(string) tokens;
	
	if (intp(var)) tokens = Parser.C.split(sprintf("%d", var));
	else if (floatp(var)) tokens = Parser.C.split(sprintf("%f", var));
	else tokens = Parser.C.split(sprintf("%O", var));
	
	if ((tokens = convert_to_json(tokens)) == 0)
		error(sprintf("JSON.serialize(%.10O) Cannot convert Pike datatype to JSON object.", var));
		
	return tokens*"";
}