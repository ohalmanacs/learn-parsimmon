<script>
	export let name;
	import Parsimmon from "parsimmon";

	var parser = Parsimmon.seq(Parsimmon.string("a"), Parsimmon.string("b").times(4));


	let inputs = ["abbbb", "abcd", "a567", "!@#"];
	let result = parser.parse(inputs[0]);
	let result2 = parser.parse(inputs[1]);
	let result3 = parser.parse(inputs[2]);
	let result4 = parser.parse(inputs[3]);
	console.log(result, result2, result3, result4);

	var numParser = Parsimmon.regexp(/[0-9]+/).map(s => Number(s));

	var numInputs = ["0", "1", "2", "3", "31", "86400", "65535", "0110110001101111"];
	var numResults1 = [];
	numInputs.forEach(inp => numResults1.push(numParser.parse(inp)));
	var numResults2 = numInputs.map(inp => numParser.parse(inp));
	console.log("##### ", numResults1, numResults2);



  function escapeSpecialCharacter(c) {
    switch (c) {
      case '"':
        return "&quot;";
      case '&':
        return "&amp;";
      case '<':
        return "&lt;";
      case '>':
        return "&gt;";
      default:
        return c;
    }
  }

  var Itero = Parsimmon.createLanguage({
    EscapedPunctuation: function() {
      return Parsimmon.string("\\")
        .then(Parsimmon.regexp(/[!"#$%&'()*+,-./:;<=>?@[\\\]^_`{|}~]/).map(s => escapeSpecialCharacter(s)))
    },
    Char: function() {
      return Parsimmon.any.map(s => escapeSpecialCharacter(s));
    },
    Bolded: function(r) {
      return Parsimmon.string('**')
        .then(r.Value.many())
        .skip(Parsimmon.string('**'))
        //.map(val => val);
    },
    Value: function(r) {
      return Parsimmon.alt(r.EscapedPunctuation, r.Bolded, r.Char);
    },
    Text: function(r) {
      return r.Value.many().map(res => res.join(''));
    }
  });


  let input = "**abcd**";
  let result = Itero.Bolded.parse(input);
  console.log("%&&%&%  (input, result) = ", input, result);

</script>

<style>
	h1 {
		color: purple;
	}
</style>

<h1>Hello {name}!</h1>

<h3>input: {inputs[0]},  result: {result.status}, {result.value}</h3>
<h3>input: {inputs[1]},  result: {result2.status}, {result2.index.column}</h3>
<h3>input: {inputs[2]},  result: {result3.status}, {result3.index.column}</h3>
<h3>input: {inputs[3]},  result: {result4.status}, {result4.index.column}</h3>
