# EXP 6 : SIMULATION OF MULTIRATE DSP WITHOUT FUNCTION

## AIM: 

To perform and verify multirate DSP without function using SCILAB.

## APPARATUS REQUIRED: 
PC installed with SCILAB. 

## PROGRAM FOR DESIMINATION:
```
clc;
clear;
close;

// --------------------------------------
// Step 1: Generate a sinusoidal signal
// --------------------------------------
N = 100;           // Number of samples
Fs = 1000;         // Sampling frequency (Hz)
f = 50;            // Signal frequency (Hz)
n = 0:N-1;         // Sample index

x = sin(2*%pi*f*n/Fs); // Sinusoidal signal

// --------------------------------------
// Step 2: Decimate the signal by factor 3
// --------------------------------------
M = 3;             // Decimation factor
x_dec = x(1:M:$);  // Take every 3rd sample
n_dec = n(1:M:$);  // Corresponding indices

// --------------------------------------
// Step 3: Plot original and decimated signals
// --------------------------------------
subplot(2,2,1);
plot2d3(n, x);
xlabel("Sample index (n)");
ylabel("Amplitude");
title("Original Sine Signal (100 samples)");

subplot(2,2,2);
plot2d3(n_dec, x_dec);
xlabel("Sample index (n)");
ylabel("Amplitude");
title("Decimated Signal (Factor = 3)");

// --------------------------------------
// Step 4: Compute frequency spectra using FFT
// --------------------------------------
X = abs(fft(x));
f_axis = (0:N-1)*(Fs/N);

N_dec = length(x_dec);
X_dec = abs(fft(x_dec));
f_axis_dec = (0:N_dec-1)*(Fs/M/N_dec); // Adjusted frequency scale

// --------------------------------------
// Step 5: Plot frequency spectra
// --------------------------------------
subplot(2,2,3);
plot(f_axis(1:N/2), X(1:N/2));
xlabel("Frequency (Hz)");
ylabel("Magnitude");
title("Spectrum of Original Signal");

subplot(2,2,4);
plot(f_axis_dec(1:N_dec/2), X_dec(1:N_dec/2));
xlabel("Frequency (Hz)");
ylabel("Magnitude");
title("Spectrum of Decimated Signal");
```

## PROGRAM FOR INTERPOLATION:
```
clc;
clear;
close;

// --------------------------------------
// Step 1: Generate a sinusoidal signal
// --------------------------------------
N = 100;           // Number of samples
Fs = 1000;         // Sampling frequency (Hz)
f = 50;            // Signal frequency (Hz)
n = 0:N-1;         // Sample index

x = sin(2*%pi*f*n/Fs); // Original sinusoidal signal

// --------------------------------------
// Step 2: Interpolate the signal by factor 2
// --------------------------------------
L = 2;                     // Interpolation factor
x_int = zeros(1, L*N);     // Initialize with zeros
x_int(1:L:$) = x;          // Insert zeros between samples
n_int = 0:length(x_int)-1; // New sample indices

// --------------------------------------
// Step 3: Plot original and interpolated signals
// --------------------------------------
subplot(2,2,1);
plot2d3(n, x);
xlabel("Sample index (n)");
ylabel("Amplitude");
title("Original Sine Signal (100 samples)");

subplot(2,2,2);
plot2d3(n_int, x_int);
xlabel("Sample index (n)");
ylabel("Amplitude");
title("Interpolated Signal (Factor = 2)");

// --------------------------------------
// Step 4: Compute frequency spectra using FFT
// --------------------------------------
X = abs(fft(x));
f_axis = (0:N-1)*(Fs/N);

N_int = length(x_int);
X_int = abs(fft(x_int));
f_axis_int = (0:N_int-1)*(Fs*L/N_int); // Adjusted frequency scale

// --------------------------------------
// Step 5: Plot frequency spectra
// --------------------------------------
subplot(2,2,3);
plot(f_axis(1:N/2), X(1:N/2));
xlabel("Frequency (Hz)");
ylabel("Magnitude");
title("Spectrum of Original Signal");

subplot(2,2,4);
plot(f_axis_int(1:N_int/2), X_int(1:N_int/2));
xlabel("Frequency (Hz)");
ylabel("Magnitude");
title("Spectrum of Interpolated Signal");
```

## PROGRAM FOR SPEECH : 

//  SPEECH RECOGNITION USING SCILAB
```
clear;
clc;

// ---------------------------
// PARAMETERS
// ---------------------------
fs = 8000;
t = 0:1/fs:0.05;     // 50 ms of signal
x = sin(2*%pi*500*t) + 0.7*sin(2*%pi*2200*t);

M = 3;   // decimation
L = 4;   // interpolation

// ---------------------------
// FIR LOWPASS FILTER (SHORT = safer)
// ---------------------------
N = 41;            // shorter filter → avoids zero output in interpolation
n = 0:N-1;
mid = (N-1)/2;

fc = 0.45*(fs/(2*M));        // cutoff before decimation
norm_fc = (2*fc)/fs;

h = zeros(1,N);
for k = 1:N
    m = k-1-mid;
    if m == 0 then
        h(k) = 1;
    else
        h(k) = sin(%pi*norm_fc*m)/(%pi*norm_fc*m);
    end
end

// Hamming window
w = 0.54 - 0.46*cos(2*%pi*(0:N-1)/(N-1));
h = h .* w;
h = h / sum(h);

// ---------------------------
// DECIMATION
// ---------------------------
x_f = conv(x, h, "same");
x_dec = x_f(1:M:$);
fs_dec = fs/M;

// ---------------------------
// INTERPOLATION (ZERO INSERTION)
// ---------------------------
x_up = zeros(1, length(x_dec)*L);
for k = 1:length(x_dec)
    x_up((k-1)*L + 1) = x_dec(k);
end

// Filtering to remove images
x_int = conv(x_up, h, "same");
fs_int = fs_dec * L;

// ---------------------------
// SAFETY CHECK (IMPORTANT)
// ---------------------------
if max(abs(x_int)) == 0 then
    disp("WARNING: Interpolated signal is zero! Filter too long or signal too short.");
end

// ---------------------------
// PLOTTING
// ---------------------------
scf(0); clf();

subplot(3,1,1);
plot(t, x);
title("Original Signal");
xlabel("Time (s)"); ylabel("Amplitude");

t_dec = 0:1/fs_dec:(length(x_dec)-1)/fs_dec;
subplot(3,1,2);
plot(t_dec, x_dec);
title("Decimated Signal");
xlabel("Time (s)"); ylabel("Amplitude");

t_int = 0:1/fs_int:(length(x_int)-1)/fs_int;
subplot(3,1,3);
plot(t_int, x_int);       // ← NOT zoomed (to avoid index errors)
title("Interpolated Signal");
xlabel("Time (s)"); ylabel("Amplitude");
```

## OUTPUT FOR DESIMINATION:
<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/15a18e8f-0c4f-45f7-b1b2-039b501429c0" />

## OUTPUT FOR INTERPOLATION:
<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/9572cd45-4fd5-4c7f-b325-8dc17a903bb7" />



## OUTPUT FOR THE SPEECH: 
<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/4cd0e9b6-6980-4d2f-b470-7539352fd74b" />



## RESULT: 
Thus the decimation process by a factor M and interpolation process by a factor L using 
SCILAB was implemented. 
