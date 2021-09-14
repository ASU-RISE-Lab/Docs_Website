---
title: Implementing PPID on Physical System
layout: tutorial
toc: true
exclude: true
permalink: /tutorials/hardware_ppid
usemathjax: true
---

Author: JJ Garrard

## Block Diagrams

An ideal $$P-PID$$ controller looks like the below block diagram.

![](/Docs_Website/docs/hardware_PPID/Block_Diagram.png)

There are some issues when implementing this on hardware:
- Step changes in $$r_x(t)$$ causes a step change in $$e_v(t)$$. The derivative of a step is infinite. This causes a large change in the control signal $$u(t)$$ until the next time step, which is bad for the actuators.
- When taking the derivative of signals containing noise, the noise is amplified. If the $$D$$ gain is too large, the noise that propogates to the control signal will be significant. The plant output will be noiser, and this feeds back to the controller and continues to be an issue.
- When the actuators are saturated and error is non-zero, the integrator accumulator will continue to grow. Once the error changes sign, the integral action can still dominate until the magnitude of the accumulator becomes reasonable.

Here are ways to mitigate the issues:
- Instead of having the derivative act on changes in error, have it act on the derivative of velocity.
- Pass the signal through a low-pass filter before taking a derivative.
- Implement an anti-windup algorithm. I will be using clamping.

The block diagram for this system looks like the following block diagram:

![](/Docs_Website/docs/hardware_PPID/Block_Diagram_clamp_filter.png)

This cascaded controller is what I want to implement.

### Low-Pass Filter (LPF)

I will be using the second-order low-pass filter of the following form:

$$H(s)=\cfrac{Y(s)}{U(s)}=\cfrac{\omega_0^2}{s^2+2s\zeta\omega_0+\omega_0^2}$$

where $$\omega_0$$ is the desired cutoff frequency and $$\zeta$$ is the damping ratio.

This not easily implementable in C/C++ or Python. I'll perform a bilinear transform using 

$$s=\cfrac{2(1-z^{-1})}{T(1+z^{-1})}$$

then perform an inverse z-transform to get the output of the signal based on the input and prior outputs.

$$\cfrac{Y(z)}{U(z)}=\cfrac{\omega_0^2}{\omega_0^2+\cfrac{4\omega_0\zeta(z-1)}{T(z+1)}+\cfrac{4(z-1)^2}{T^2(z+1)^2}}$$

$$\frac{Y\left(z\right)}{U\left(z\right)}=\frac{\left(T^2\,\omega_0 ^2\right)\,z^2+\left(2\,T^2\,\omega_0 ^2\right)\,z+T^2\,\omega_0 ^2}{\left(T^2\,\omega_0 ^2+4\,\zeta \,T\,\omega_0 +4\right)\,z^2+\left(2\,T^2\,\omega_0 ^2-8\right)\,z+T^2\,\omega_0 ^2-4\,\zeta \,T\,\omega_0 +4}$$

$$[(T^2\omega_0^2+4\zeta T\omega_0+4)z^2+(2T^2\omega_0^2-8)z+T^2\omega_0^2-4\zeta T\omega_0 +4]Y(z)=[(T^2\omega_0^2)z^2+(2T^2\omega_0^2)z+T^2\omega_0^2]U(z)$$

$$(T^2\omega_0^2+4\zeta T\omega_0+4)y[k+2]+(2T^2\omega_0^2-8)y[k+1]+(T^2\omega_0^2-4\zeta T\omega_0 +4)y[k]=(T^2\omega_0^2)u[k+2]+(2T^2\omega_0^2)u[k+1]+T^2\omega_0^2u[k]$$

$$(T^2\omega_0^2+4\zeta T\omega_0+4)y[k+2]=(T^2\omega_0^2)u[k+2]+(2T^2\omega_0^2)u[k+1]+T^2\omega_0^2u[k]-(2T^2\omega_0^2-8)y[k+1]-(T^2\omega_0^2-4\zeta T\omega_0 +4)y[k]$$

$$y[k+2]=\cfrac{1}{T^2\omega_0^2+4\zeta T\omega_0+4}[(T^2\omega_0^2)u[k+2]+(2T^2\omega_0^2)u[k+1]+T^2\omega_0^2u[k]-(2T^2\omega_0^2-8)y[k+1]-(T^2\omega_0^2-4\zeta T\omega_0 +4)y[k]]$$

$$y[k]=\cfrac{1}{T^2\omega_0^2+4\zeta T\omega_0+4}[(T^2\omega_0^2)u[k]+(2T^2\omega_0^2)u[k-1]+T^2\omega_0^2u[k-2]-(2T^2\omega_0^2-8)y[k-1]-(T^2\omega_0^2-4\zeta T\omega_0 +4)y[k-2]]$$

The above equation is the low-pass filter that I will implement in software. Before doing so, I want to see what the response is like.

Below is the MATLAB code that plots the input and output of the LPF. 

```matlab
T = 0.01;   % Time between samples in s
w = 10*2*pi;     % Cutoff frequency in rad/s
z = 1;      % Damping ratio
tf = 5;    % Run simulation for this many seconds

% Initialization
u = [0, 0, 0];
y = [0, 0, 0];
u_hist = [];
y_hist = [];

% Write out denominator for convenience
den = 1/(T^2*w^2+4*T*z*w+4);

% Main loop
for i = 1:tf/T
    t = i*T;
    u(3) = u(2);
    u(2) = u(1);
    u(1) = sin(10*t);
    y(3) = y(2);
    y(2) = y(1);
    y(1) = den*(T^2*w^2*u(1)+2*T^2*w^2*u(2)+T^2*w^2*u(3)-(2*T^2*w^2-8)*y(2)-(T^2*w^2-4*z*T*w+4)*y(3));
    u_hist(i) = u(1);
    y_hist(i) = y(1);
end

% Plotting
figure();
hold on;
grid on;
grid minor;
xlabel("Time (s)");
ylabel("Input, Output");
plot(0:T:T*size(y_hist,2)-T,u_hist)
plot(0:T:T*size(y_hist,2)-T,y_hist)
legend("Input", "Output",'Interpreter','latex');

% Make lines and font larger
set(findall(gcf,'-property','FontSize'),'FontSize',20)
set(findall(gcf,'-property','Interpreter'),'Interpreter','Latex')
set(findall(gcf, 'Type', 'Line'),'LineWidth',3);
```

Here is the corresponding plot:
![img.png](/Docs_Website/docs/hardware_PPID/img.png)