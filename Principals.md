# Principle of Operation

This document outlines the core workflow for the beat trainer system, which detects tempo and musical phrases in audio input using signal processing and neural networks.

1. **Analyze the spectrum of the input signal (FFT):**  
    The system first processes the incoming audio using a Fast Fourier Transform (FFT) to extract frequency domain information, focusing on the frequency range most relevant to rhythm detection.

2. **Neural network analysis:**  
    The extracted FFT data is fed into a neural network, which has been trained to recognize rhythmic patterns and phrase boundaries.

3. **Neural network output:**  
    The neural network produces two outputs:  
    - **Beat detection:** Identifies the timing of beats in the audio.  
    - **Phrase detection:** Identifies the start of musical phrases.

4. **Calculate tempo and phrase step:**  
    Using the detected beats and phrase starts, the system calculates the current tempo (beats per minute) and the position within the musical phrase.

5. **Start the rhythm:**  
    The system can trigger a metronome or other rhythmic output in sync with the detected tempo and phrase.

6. **Adjust rhythm dynamically:**  
    If the tempo or rhythmic pattern changes in the input, the system adapts in real time to maintain synchronization.

# FFT Parameters

- **Range:** 40-400 Hz  
  This frequency range covers the fundamental frequencies of most percussive and rhythmic elements in music, such as kicks, snares, and hi-hats.

- **Window:** 256 bins  
  The FFT operates on windows of 256 samples, providing a balance between frequency resolution and time responsiveness.

- **Frequency:** 100 Hz (can be faster - needs testing)  
The FFT is computed 100 times per second, allowing for near real-time analysis. This rate may be increased for finer temporal resolution if needed, or reduced if performance problems are observed.

# Neural Network

## Architecture

- **Inputs:**  
     - **A:** Matrix of size X×Y (to be specified) containing the N most recent FFT result samples, e.g., `samples[-N:-2]`. This provides temporal context for the network to analyze rhythmic patterns over time.
     - **B:** The most recent FFT sample, `samples[-1]`, giving the network immediate information about the current audio frame.

- **Outputs:**  
     - **A:** Beat/pulse detection signal (binary or probability), indicating whether a beat occurs at the current time step.
    - **B:** Rhythmic unit start signal, indicating the beginning of a new musical phrase.  
    - **C:** Weak/strong beat classification signal, indicating the relative strength of the detected beat (e.g., distinguishing downbeats from upbeats) to help determine the rhythmic pattern.  
        > **Note:** While the neural network can be trained to estimate beat strength, reliably distinguishing weak and strong beats from audio alone may be challenging and depends on the quality of training data and network design.

## Training Method

1. **Track analysis:**  
    Collect a dataset of 100-200 music tracks covering a variety of genres and tempos.

2. **Manual labeling:**  
    Annotate each track with ground truth tempo and phrase boundaries to provide supervised learning targets.

3. **Sample slicing:**  
    Divide the audio into 5-10 second segments, including periods of silence, to ensure the network learns to handle both active and inactive sections.

4. **Network training:**  
    Feed the labeled samples into the neural network and train it to predict beats and phrase starts from the FFT data.

5. **Testing:**  
    Evaluate the trained network on unseen tracks to verify its accuracy and generalization to new audio material.