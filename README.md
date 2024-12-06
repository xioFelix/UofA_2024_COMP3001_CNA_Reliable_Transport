# Alternating Bit Protocol Assignment

You have been tasked by "Net Source" to finish the implementation of the Alternating Bit protocol. The original programmer, Mue, has left partially completed code and comments indicating what still needs to be done. Your job is to complete the implementation, ensure the protocol’s correctness, and test it thoroughly.

**Key points:**
- The protocol implementation follows the Alternating Bit design as described in the textbook (rdt3.0, pages 244/245).
- The code is in C, but you are only expected to correct protocol logic errors, not C syntax errors.
- A simulation environment is provided to test sending messages from host A to host B, using a layered approach and an unreliable network simulation.
- Your changes should be confined to the `altbit.c` file only. Do not modify other files.

---

## Environment and Files

There are four files involved:

- `emulator.c`
- `emulator.h`
- `altbit.c` (this is where you will make changes)
- `altbit.h`

**Do not modify `emulator.c`, `emulator.h`.** These files provide the simulation environment and are correct as given. You only need to fix and complete the Alternating Bit protocol code in `altbit.c`.

**Compilation:**
```bash
gcc -ansi -Wall -pedantic emulator.c altbit.c
```
The above command produces an executable `a.out`.

**Running the emulator:**
```bash
./a.out
```
You will be prompted for network parameters:
- Number of messages to simulate.
- Packet loss probability.
- Packet corruption probability.
- Tracing level (0 to 2+; higher values give more detail).
- Average time between messages from the sender’s layer 5.

Experiment with different parameters to understand the current (buggy) behavior.

---

## The Alternating Bit Protocol

**Data structures:**

- `struct msg`:
  ```c
  struct msg {
    char data[20];
  };
  ```
  Used for application-layer data passed to the transport layer.

- `struct pkt`:
  ```c
  struct pkt {
    int seqnum;
    int acknum;
    int checksum;
    char payload[20];
  };
  ```
  Used for communication between the transport layer and the network layer. The sender will set `payload`, `seqnum`, and compute a `checksum`. The receiver sets `acknum` and `checksum` for acknowledgements.

**Provided routines you must not modify:**

- `starttimer(calling_entity, increment)`
- `stoptimer(calling_entity)`
- `tolayer3(calling_entity, packet)`
- `tolayer5(calling_entity, message)`

**Routines you must finalize in `altbit.c`:**
- **A-side**: `A_output()`, `A_input()`, `A_timerinterrupt()`, `A_init()`
- **B-side**: `B_input()`, `B_init()`

**Important:** The protocol is unidirectional from A to B. B sends ACKs back to A. No B_timerinterrupt() or B_output() is needed.

**Your job:**  
Finish and correct the Alternating Bit protocol according to the provided finite state machine (rdt3.0). Ensure the following:
- Proper handling of timeouts (A retransmits when timer expires).
- Correct processing of ACKs (only accept ACK if it is for the currently outstanding packet).
- Proper handling of corrupted or lost packets.
- The receiver (B) must deliver correct packets in order and discard/correctly handle duplicates, corrupted packets, or out-of-order packets.
  
---

## Steps and Testing Strategy

1. **Basic scenario (No loss, no corruption, single packet)**  
   Test sending a single packet with no loss/corruption. Verify correct behavior (A sends one packet, B receives and ACKs it, A stops timer and moves on).  
   Once correct, you should pass Test 1.

2. **Handle packet loss**  
   Implement and fix the parts of the code that handle retransmission after timeouts. When a packet or ACK is lost, A should timeout and retransmit.  
   After fixing, you should pass Test 2.

3. **Handle duplicate ACKs and out-of-order packets**  
   Implement the logic for detecting duplicate ACKs at A and rejecting out-of-order or unexpected packets at B.  
   After these fixes, you should pass Test 3.

4. **Handle corruption**  
   Implement the logic for detecting corrupted packets or ACKs and reacting accordingly.  
   After this, you should pass Test 4.

**Note:** The protocol’s correctness can be validated by running tests with various levels of loss and corruption. Increase tracing level to 2 for detailed debugging information.

---

## Testing with the Oracle

Two submission links are provided:
- **Main Submission**: `<year>/<semester>/cna/AlternatingBit`  
  Use this link for final assessment. It gives you a score based on tests passed.
  
- **Oracle Submission**: `<year>/<semester>/cna/AlternatingBit-Oracle`  
  Use this to get detailed feedback and understand where your implementation differs from the expected output.  
  **Important:** Submissions to the Oracle beyond the second attempt will cost a 1-mark penalty each time.

**Strategy:**
- Develop and test locally.
- Submit to the Oracle if you are stuck. Use its detailed feedback to correct your implementation.
- Finalize and submit for final grading via the main submission link.

---

## Additional Tips

- Use `-ansi -Wall -pedantic` flags when compiling to detect warnings. Fix all warnings before submission.
- Test incrementally:
  - First, ensure correct behavior with no loss/corruption.
  - Introduce loss and verify retransmissions.
  - Introduce corruption and verify detection and handling.
- Keep an SVN log of your changes and insights.

**Remember:** Debugging network protocols can be tricky. Use the Oracle output to see what differs from the expected behavior. Review the rdt3.0 FSM carefully. Make sure you follow every step of the protocol as specified.