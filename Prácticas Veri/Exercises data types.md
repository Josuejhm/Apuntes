1.  a. De 0 a 7.
	b. 000_1111_0000_0000. Esto porque int es un tipo de 2 estados (0 y 1), por lo que los valores x y z se convierten a 0.
	c. 32768.
	d. -32768. Porque `shortint` es signed de 16 bits.
	e. 32767. Porque un `shortint` solo puede representar de -32768 a 32767, por lo que -32768 - 1 = -32769 produce un overflow.

2.  a. x, pero como bit es 2-state, entonces es `my_mem[2] = 8'h00`.
	b. `my_logic = xxxx`, porque logic es 4-state
	c. `my_mem[3] = 8'hA5`. Truncando a 4 bits -> `my_mem[3] = 4'h5`.
	d. `my_mem[3] = 8'h00`. Son x, pero my_mem es 2-state.
	e. `my_logic = 4'h1`.
	f. `my_logic = 4'h5`. Porque se trunca.
	g. `my_logicmem[4] = xxxx` -> `my_logicmem[xxxx] = xxxx` -> `my_logic = xxxx`.

3. 
```systemverilog
bit [11:0] my_array[4];

initial begin

	my_array = '{12'h012,12'h345,12'h678,12'h9AB};

	for (int i = 0; i < 4; i++) begin
		$display("my_array[%0d][5:4] = 0x%0h", i, my_array[i][5:4]);
	end
	
	foreach (my_array[i]) begin
		$display("my_array[%0d][5:4] = 0x%0h", i, my_array[i][5:4]);
	end

end
```

4.  El array quedaría así:
```systemverilog
logic my_array1 [5][31];
```
La única asignación válida es:
```systemverilog
my_array1[4][30] = 1'b1;
```
La segunda no concuerda con las dimensiones y la tercera no se puede porque se está seleccionado toda la fila 4 la cual tiene 31 bits en total, no 32.

5. El array quedaría así:
```systemverilog
bit my_array2 [5][31];
```
Como se mantienen prácticamente los mismos casos que el ejercicio anterior, la respuesta sería la misma por las mismas razones. Esto porque 2-state no modifica las dimensiones del array, solo el tipo de dato.

6.  Respuesta:
```systemverilog
Street[0] = Tejon
Street[2] = Platte
Street[2] = Bijou
pop_back = Boulder
street.size = 4
```

7.  Respuesta:
```systemverilog
module seven;

	bit [23:0] mem [bit[29:0]];

	initial begin

		// Reset
		mem[0] = 24'h50400;      // PC starts at address 0 at reset

		// Main code
		mem[32'h400] = 24'h123456;
		mem[32'h401] = 24'h789ABC;

		// Return
		mem[30'h3FFFFFFF] = 24'h0F1E2D;

		foreach(mem[i]) begin
			$display("Address = %d, Instr = 0x%0h", i, mem[i]);
		end

		$display("Number of elements = %d", mem.size());

	end
endmodule
```

8. Respuesta:
```systemverilog
module eight;

	int [23:0] q1[$] = {2, -1, 127};

	int sum = 0;
	int idx_q1_n[$];
	int p_values_q1[$];

	initial begin
		foreach(q1[i]) begin
			sum += q1[i];
		end
		$display("Sum = %0d", sum);

		$display("Max = %0d", q1.max());
		$display("Max = %0d", q1.min());
		$display("q1 sorted = %p", q1.sort());

		foreach(q1[i]) begin
			if(q1[i] < 0) begin
				idx_q1_n.push_back(i);
			end
		end
		$display("Indexes with negative values: %p", idx_q1_n);

		foreach(q1[i]) begin
			if(q1[i] > 0) begin
				p_values_q1.push_back(q1[i]);
			end
		end
		$display("Positive values in the queue: %p", p_values_q1);

		$display("q1 reverse sorted = %p", q1.rsort());
	end
endmodule
```

9. Respuesta:
```systemverilog
typedef logic [6:0] value_t;

typedef struct packed {
	value_t header;
	value_t cmd;
	value_t data;
	value_t crc;
} packed_structure_s;

module nine;
	packed_structure_s ps;
	
	initial begin
		ps.header = 7'h5A;
	end
endmodule
```

10. Respuesta:
```systemverilog
module ten;
	typedef logic [3:0] nibble_t;
	
	real r = 4.33;
	shortint i_pack;
	nibble_t k[4] = '{4'h0,4'hF,4'hE,4'hD}

	initial begin
		$display("Unpacked array k = %p", k);
		
		i_pack = {<<{k}};
		$display("k streamed (bit basis) = %h", i_pack);

		i_pack = {<<4{k}};
		$display("k streamed (nibble_t basis) = %h", i_pack);

		k[0] = nibble_t'(r);
		$display("k with r = %f converted to nibble = %p", r, k);
	end
endmodule
```

11.  Respuesta:
```systemverilog
`timescale 1ns/1ps

typedef enum logic [1:0] {
	Add = 2'b00,
	Sub = 2'b01,
	Bit_wise_invert = 2'b10,
	Reduction_Or = 2'b11
} opcode_e;

module alu (
	input logic [7:0] A,
	input logic [7:0] B,
	input opcode_e opcode,

	output logic [7:0] result

);

	always_comb begin
		case (opcode)
			Add: result = A + B;
			Sub: result = A - B;
			Bit_wise_invert: result = ~A;
			Reduction_Or: result = |B;
			default: result = 8'h00;
		endcase
	end
endmodule

module tb_eleven;
	logic [7:0] A;
	logic [7:0] B;
	opcode_e opcode;
	
	logic [7:0] result;
	
	alu dut (
		.A(A),
		.B(B),
		.opcode(opcode),
		.result(result)
	);

	initial begin
		A = 8'hAB;
		B = 8'hBA;
		
		foreach(opcode_e'(i)) begin
			opcode = i;
			#10;
			$display("Opcode = %b, Result = %h", opcode, result);
		end

		$finish;
	end
endmodule
```