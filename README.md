# EXP 6 : SIMULATION OF MULTIRATE DSP WITHOUT FUNCTION

## AIM: 

To perform and verify multirate DSP without function using SCILAB.

## APPARATUS REQUIRED: 
PC installed with SCILAB. 

## PROGRAM : 

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


## OUTPUT: 
<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/4cd0e9b6-6980-4d2f-b470-7539352fd74b" />



## RESULT: 
Thus the decimation process by a factor M and interpolation process by a factor L using 
SCILAB was implemented. 
