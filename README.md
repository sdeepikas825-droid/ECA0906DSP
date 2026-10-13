clear;
clc;
close all;

% EXPERIMENT 1: BUTTERWORTH IIR LOW-PASS AND HIGH-PASS FILTERS

% Design Parameters
Fs = 8000;       % Sampling frequency in Hz
Ap = 1;          % Maximum passband ripple in dB
As = 40;         % Minimum stopband attenuation in dB

%% 1. LOW-PASS FILTER DESIGN

fp_lp = 1000;    % Passband frequency in Hz
fs_lp = 1500;    % Stopband frequency in Hz

% Normalize frequencies (Nyquist frequency = Fs/2)
wp_lp = fp_lp / (Fs/2);
ws_lp = fs_lp / (Fs/2);

% Calculate filter order and cutoff frequency
[N_lp, Wn_lp] = buttord(wp_lp, ws_lp, Ap, As);

% Design Butterworth low-pass filter
[b_lp, a_lp] = butter(N_lp, Wn_lp, 'low');

%% 2. HIGH-PASS FILTER DESIGN

fp_hp = 2500;    % Passband frequency in Hz
fs_hp = 1800;    % Stopband frequency in Hz

% Normalize frequencies
wp_hp = fp_hp / (Fs/2);
ws_hp = fs_hp / (Fs/2);

% Calculate filter order and cutoff frequency
[N_hp, Wn_hp] = buttord(wp_hp, ws_hp, Ap, As);

% Design Butterworth high-pass filter
[b_hp, a_hp] = butter(N_hp, Wn_hp, 'high');

%% 3. DISPLAY NUMERICAL RESULTS

fprintf('============================================\n');
fprintf(' BUTTERWORTH IIR FILTER DESIGN\n');
fprintf('============================================\n\n');

fprintf('--- LOW-PASS FILTER ---\n');
fprintf('Filter Order       : %d\n', N_lp);
fprintf('Cutoff Frequency   : %.2f Hz\n', Wn_lp * Fs/2);
fprintf('Numerator Coefficients (b_lp):\n');
disp(b_lp);

fprintf('Denominator Coefficients (a_lp):\n');
disp(a_lp);

fprintf('\n--- HIGH-PASS FILTER ---\n');
fprintf('Filter Order       : %d\n', N_hp);
fprintf('Cutoff Frequency   : %.2f Hz\n', Wn_hp * Fs/2);
fprintf('Numerator Coefficients (b_hp):\n');
disp(b_hp);

fprintf('Denominator Coefficients (a_hp):\n');
disp(a_hp);

%% 4. FREQUENCY RESPONSE CALCULATION

[H_lp, f] = freqz(b_lp, a_lp, 2048, Fs);
[H_hp, ~] = freqz(b_hp, a_hp, 2048, Fs);

% Convert magnitude to dB safely
mag_lp_db = 20*log10(max(abs(H_lp), 1e-12));
mag_hp_db = 20*log10(max(abs(H_hp), 1e-12));

% Phase response in degrees
phase_lp = unwrap(angle(H_lp)) * 180/pi;
phase_hp = unwrap(angle(H_hp)) * 180/pi;

%% 5. PLOT FREQUENCY RESPONSES

figure('Name', 'Butterworth IIR Filters', ...
       'NumberTitle', 'off');

% Low-pass magnitude response
subplot(2,2,1);
plot(f, mag_lp_db, 'b', 'LineWidth', 1.5);
grid on;
title(sprintf('Low-Pass Magnitude (Order N = %d)', N_lp));
xlabel('Frequency (Hz)');
ylabel('Magnitude (dB)');
xlim([0 Fs/2]);
ylim([-100 5]);

% Low-pass phase response
subplot(2,2,2);
plot(f, phase_lp, 'r', 'LineWidth', 1.5);
grid on;
title('Low-Pass Phase Response');
xlabel('Frequency (Hz)');
ylabel('Phase (degrees)');
xlim([0 Fs/2]);

% High-pass magnitude response
subplot(2,2,3);
plot(f, mag_hp_db, 'b', 'LineWidth', 1.5);
grid on;
title(sprintf('High-Pass Magnitude (Order N = %d)', N_hp));
xlabel('Frequency (Hz)');
ylabel('Magnitude (dB)');
xlim([0 Fs/2]);
ylim([-100 5]);

% High-pass phase response
subplot(2,2,4);
plot(f, phase_hp, 'r', 'LineWidth', 1.5);
grid on;
title('High-Pass Phase Response');
xlabel('Frequency (Hz)');
ylabel('Phase (degrees)');
xlim([0 Fs/2]);

sgtitle('Butterworth IIR Low-Pass and High-Pass Filter Responses');

%% 6. EXPERIMENT RESULT

fprintf('\nExperiment completed successfully.\n');
fprintf('Low-pass and high-pass filters designed and plotted.\n');
