### **Day 43: Coverage-Driven Verification – Basics**

---

### **1. Introduction**

In SystemVerilog, **coverage-driven verification (CDV)** is a methodology used to **measure how much of the design has been exercised** by testbenches. It provides **feedback to guide stimulus generation** so that important parts of the design are not missed during verification.

SystemVerilog provides two main types of coverage:
- **Code coverage** – monitored by simulators (e.g., line, toggle, FSM).
- **Functional coverage** – written by verification engineers using **covergroups**.

We will focus on **functional coverage** here.

---

### **2. What Is Functional Coverage?**

Functional coverage is a way to **track whether specific values or combinations of values** have occurred during simulation.

This is done using **covergroups**, which can include:
- **Coverpoints** – track values of variables.
- **Cross coverage** – track combinations of variables.

---

### **3. Covergroup Basics**

#### **Syntax:**

```systemverilog
covergroup group_name;
    coverpoint variable_name;
endgroup
```

#### **Instantiation:**

```systemverilog
group_name cg = new();  // Create covergroup object
cg.sample();            // Sample values
```

---

### **4. Example: Functional Coverage of a Packet**

```systemverilog
class Packet;
    rand bit [3:0] addr;
    rand bit [7:0] data;

    // Covergroup declaration
    covergroup packet_cg;
        coverpoint addr;     // Covering all values of addr
        coverpoint data {
            bins low  = {[0:63]};
            bins high = {[64:127]};
        }
    endgroup

    function new();
        packet_cg = new();   // Construct covergroup
    endfunction

    function void sample();
        packet_cg.sample();  // Sample current values
    endfunction

endclass
```

---

### **5. Testbench to Drive and Sample Coverage**

```systemverilog
module tb;

    Packet pkt;

    initial begin
        pkt = new();

        repeat (10) begin
            pkt.randomize();
            pkt.sample();  // Sample values into coverage
            $display("Randomized addr = %0d, data = %0d", pkt.addr, pkt.data);
        end
    end

endmodule
```

---

### **6. Viewing Coverage**

To view coverage results:
- Use your **simulator’s GUI** (like Questa, VCS, or Xcelium).
- Most tools provide **HTML or waveform-based coverage reports**.

---

### **7. Cross Coverage**

To measure combinations:

```systemverilog
covergroup cg;
    coverpoint addr;
    coverpoint data;
    cross addr, data;
endgroup
```

Cross coverage tracks if every combination of `addr` and `data` occurs.

---

### **8. Coverage Bins**

You can define bins to group values or ranges:

```systemverilog
coverpoint mode {
    bins idle = {0};
    bins active = {[1:3]};
    bins error = {4, 5};
}
```

---

### **9. Summary**

| Concept           | Purpose                           |
|-------------------|------------------------------------|
| Covergroup        | Container for coverage logic       |
| Coverpoint        | Track values of a variable         |
| Cross             | Track combinations of values       |
| sample()          | Collect current values             |
| Bins              | Group values into ranges or sets   |

