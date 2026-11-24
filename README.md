# EXP-5-DFF-TFF
# Design and Verification of D Flip-Flop and T Flip-Flop
## Aim
To design and verify a D Flip-Flop and a T Flip-Flop using Verilog HDL, and to observe their behaviour using a testbench and VCD waveform.
## Theory / Synopsis
A D Flip-Flop (Data / Delay Flip-Flop) is an edge-triggered sequential element that transfers the value present at its input D to the output Q on the active (rising) edge of the clock. It is commonly used to store one bit of data and eliminates the race-around problem seen in level-triggered latches.
A T Flip-Flop (Toggle Flip-Flop) is an edge-triggered sequential element whose output toggles (complements) on every active clock edge when the toggle input T is high, and holds its previous state when T is low. It is widely used in counter and frequency-divider circuits.
Both flip-flops used here are also provided with an active-high asynchronous reset that forces Q to 0 irrespective of the clock.
## Boolean / Characteristic Equations
### D Flip-Flop
Q(next) = D
### T Flip-Flop
Q(next) = T ⊕ Q   (Q(next) = Q if T = 0, Q(next) = Q' if T = 1)
## Files to be Created
<img width="625" height="50" alt="image" src="https://github.com/user-attachments/assets/65034a51-5642-46ac-940a-f7ee975cea9c" />

## Design / RTL Program
// gedit up_counter.v
 
module up_counter (
    input  wire       clk,    // clock
    input  wire       rst,    // asynchronous active-high reset
    input  wire       en,     // count enable
    output reg  [3:0] count   // 4-bit count output
);
    always @(posedge clk or posedge rst) begin
        if (rst)
            count <= 4'b0000;
        else if (en)
            count <= count + 1'b1;   // increment, wraps 1111 -> 0000
        else
            count <= count;          // hold
    end
endmodule

## Testbench Program
// gedit tb7.v
 
module tb7;
    reg  clk, rst, en;
    wire [3:0] count;
 
    // Instantiate the up-counter
    up_counter uut (
        .clk(clk),
        .rst(rst),
        .en(en),
        .count(count)
    );
 
    // ---- Clock generation: 10 time-unit period ----
    initial clk = 0;
    always #5 clk = ~clk;
 
    initial begin
        // ---- VCD dump setup ----
        $dumpfile("up_counter.vcd");   // name of the VCD file to be generated
        $dumpvars(0, tb7);               // dump all signals in this testbench hierarchy
 
        $monitor("Time=%0t rst=%b clk=%b en=%b | count=%b (%0d)",
                  $time, rst, clk, en, count, count);
 
        // ---- Apply asynchronous reset ----
        rst = 1; en = 0;
        #7  rst = 0;
 
        // ---- Disable counting: count should hold at 0 ----
        @(negedge clk);
        @(negedge clk);
 
        // ---- Enable counting and let it run past overflow ----
        en = 1;
        repeat (18) @(negedge clk);   // 18 clocks: 4-bit count wraps around once
 
        // ---- Disable again and confirm the count holds ----
        en = 0;
        @(negedge clk);
        @(negedge clk);
 
        // ---- Apply reset again mid-simulation ----
        rst = 1;
        @(negedge clk);
        rst = 0; en = 1;
        repeat (3) @(negedge clk);
 
        #20 $finish;
    end
endmodule



## Truth Table

<img width="349" height="264" alt="image" src="https://github.com/user-attachments/assets/d05e6f9d-ff68-466e-9eb8-52e22fa1b961" />

## Simulation Procedure

STEP 1 – Open Terminal

Open a terminal in the experiment folder.

STEP 2 – Load Synopsys Environment
source /synopsys/start.sh

STEP 3 – Compile Using VCS
vcs dff_tff.v tb.v -full64
If compilation is successful, VCS generates the simulation executable:
simv

STEP 4 – Run Simulation
./simv
The terminal displays the reset, clock, D/T inputs and the corresponding outputs Qd and Qt at every change (via $monitor). A VCD waveform file is also generated:
dff_tff.vcd

STEP 5 – Open DVE
dve -full64
Other option:
dve -full64 &
A DVE environment will open.
## OUTPUT

## DVE Waveform Verification
<img width="1920" height="1080" alt="Screenshot (106)" src="https://github.com/user-attachments/assets/fc8b6169-6294-46ad-a006-a8cce1105d52" />
<img width="1920" height="1080" alt="Screenshot (107)" src="https://github.com/user-attachments/assets/cae60fe6-9bd3-4f92-91d8-86ad9b46fd8f" />



## Expected Result

<img width="1920" height="1080" alt="Screenshot (105)" src="https://github.com/user-attachments/assets/0d372833-91d3-4b1a-a458-5f7c75938b5f" />






## Result

Thus, the D Flip-Flop and T Flip-Flop were designed, implemented using Verilog HDL, successfully simulated using Synopsys VCS, and verified using Synopsys DVE.
