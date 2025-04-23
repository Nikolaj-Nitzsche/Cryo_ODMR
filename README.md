# Cryo_ODMR
Script library for cryo-ODMR setup

Ownership goes to the Ishihara lab at TUDelft Qutech. Not allowed to use or distribute without the consent of the owner N.K.Niztsche.

Scripts-----

%!TEX root = ../thesis.tex
% ******************************* Thesis Appendix A ****************************
\chapter{Python control scripts} 
\label{code}
\section*{Linux OS Python Scripts}
Here follows an explanation section of all the available Python scripts made for the Ubuntu PC. These can also be found in the following GitHub: 
\newline
\href{https://github.com/Nikolaj-Nitzsche/Cryo\_ODMR.git}{https://github.com/Nikolaj-Nitzsche/Cryo\_ODMR.git}.
\newline
Additionally, scripts can be found on the lab Ubuntu PC at >> dl-lab-pc3 >> Documents >> Nikolaj\_Nitzsche.

\begin{itemize}
    \item \textbf{CMOS\_sweep.py} This file initiates connection with the R\&S AWG sweeping over the specified bandwidth, plotting PL versus frequency, thus creating ESR measurements. 
    \item \textbf{CMOS\_sweep\_x.py} This file performs sweeps using the \texttt{CMOS\_sweep.py} structure but in the style of an x-scan, moving to several points in the x direction. Showing the different ESR scans in the x direction.
    \item \textbf{Data\_diff.py} In the case of measurements being done with e.g. RF\_on or RF\_off, or at RT or CT, it is useful to see the difference between two measurements. This file takes two input folders and plots the difference in PL in a third given output folder. The output folder has to be created beforehand.
    \item \textbf{Data\_merger.py} When measurements fail due to the piezo stage crashing, the measurement can be restarted from where it left off. Using this script, two (or several) measurement folders can be merged, creating a single processable file.
    \item \textbf{Noise\_CMOS\_sweep.py} A similar file to \texttt{CMOS\_sweep.py}, but now using a reference frequency to normalize the total scan, combating oscillations and noise during the measurement.
    \item \textbf{PL\_2D.py} Older version of the \texttt{PL\_2D\_test.py} file, connecting to the ZI AWG. The method of piezo glitch handling is moving it back to the center of the piezo stages and trying to move back to the right position again. This works occasionally, but mostly would just try to move over and over again until the source code timeout was called, stopping the measurement. 
    \item \textbf{PL\_2D\_mapping.py} The main 3D PL measurement script. It has no arguments because all the processing is done using the \texttt{PL\_x\_process.py} script. Error handling of the piezo is done using the \texttt{fix-atocube} option, where a new library is initialized for the attocube controller, solving the problem with the piezo stage glitching out. The steps after which this "piezo rest" process is started can be set in the settings. However, due to the speed of this process, it is advisable to keep the number of steps relatively low, e.g., 5 y-steps, decreasing the overall drift. 
    \item \textbf{PL\_2D\_process.py} Process file for 2D PL scans, based on the old already available processing file. It still contains some processing code for ESR dip fits and strain measurements, in case that needs to be used in the future.
    \item \textbf{PL\_2D\_test.py} Main version for 2D PL scans at the time of writing. Contains all the argumentation to alter different measurement sequences, such as a z-shape or a meandering motion. Uses the same callable \texttt{fix-atocube} script for glitch error handling. 
    \item \textbf{PL\_test.py} A code that can be used to move the piezo stages to specific locations without having to do so in a Python terminal.
    \item \textbf{PL\_vs\_time.py} A script keeping track of the PL for a specific amount of time, used for characterizing the PL oscillations discussed in section \ref{ESR_code}. It uses the ZI AWG on channel 4.
    \item \textbf{PL\_x\_process.py} Main processing code for most scripts, but mostly used for \texttt{x\_scan\_Cryo.py} and \texttt{2D\_PL\_mapping.py}. It has a plethora of arguments that are all described in the -h section, i.e., a list of all the argument possibilities is shown by running the following line in a terminal directory where these files are located.
    \begin{verbatim}
        python3 PL_x_process.py -h
    \end{verbatim}
    This can be done with all scripts written, with the exception of \texttt{PL\_2D\_mapping.py}, which does not have any arguments.
    \item \textbf{Rabi.py} Rabi oscillations have been attempted but to no avail. This code creates an initialization green laser pulse, RF driving sweep, and finally a green readout laser pulse. This is followed by a PL instance measurement and plotting this versus the different RF driving time.
    \item \textbf{RF\_on\_script.py} Some of the codes have a built-in RF toggle switch as arguments. But not all, so for those who do not have that option, this script turns the R\&S AWG on at a specific frequency (standard 2.87 GHz). It can be run like all other scripts and stopped using ctrl + c. The AWG will be on as long as the process is not interrupted using ctrl + c. 
    \item \textbf{rs\_base\_signal\_gen.py} QMI base class for R\&S AWG instrument driving.
    \item \textbf{sma100b.py} Old QMI version of \texttt{rs\_base\_signal\_gen.py}
    \item \textbf{start\_remote\_swabian\_server.py} Starts the Swabian server. Must be done in a separate terminal and can be done by simply:
    \begin{verbatim}
        python3 start_remote_swabian_server.py
    \end{verbatim}
    Followed by ctrl + c, or ctrl + shift + $\backslash$ to stop the server. Ctrl + c is the better option. The computer has crashed before because of the other combination being used too much (several days without restarting the PC).
    \item \textbf{tilt\_calc.py} Calculates the tilt based on 4 measurement points. I found that using dx/dy (top) and dx/dz (right) gave the best results for ay and az, respectively.
    \item \textbf{x\_scan\_Cryo.py} This is the script that runs the x-scan. Arguments allow for more options during and after the measurement.
\end{itemize}

\section{Error handling}
Several strange errors and problems have been seen during this project. I will list a few solutions that can be used in case of these different problems.
\subsection{Piezo}
A frequent error that can occur looks something in the trans of \texttt{USB-connection-timeout} or \texttt{USB-busy}. The first solution should be to try and run the \texttt{fix-atocube} option by running this straight into a free terminal, no python3 in front is needed. Otherwise, unplugging the USB directly and re-inserting it also sometimes solves the problem. If one is not close the the Linux PC itselff, the following code can be run to try and diconnect and reconnect the USB conenction:
\begin{verbatim}
#Diconnecting driver

    echo -n "1-10.2.1.1" | sudo tee /sys/bus/usb/drivers/usb/unbind
    echo 0 | sudo tee /sys/bus/usb/devices/1-10.2.1.1/authorized
    lsusb | grep 16c0:055b
    
#Reconnecting driver

    echo 1 | sudo tee /sys/bus/usb/devices/1-10.2.1.1/authorized
    echo -n "1-10.2.1.1" | sudo tee /sys/bus/usb/drivers/usb/bind
\end{verbatim}
Finally, this can also occur if the piezo stage is being controlled from an open Python terminal somewhere. The piezo can be run using the terminal as follows:
\begin{verbatim}
    directory$ python3
    >>> from pylablib.devices import Attocube
    >>> atc = Attocube.ANC350()
    >>> atc.move_to(0,0.003000)
    >>> exit()
\end{verbatim}
These lines should be run one after the other. It will first open a Python shell, initialize the piezo and then move the x axis to 3000 $\mu$m. Finally, once done, the shell can be closed using \texttt{exit()}
\newline
If the limit of the piezo stage is changing over time, the best thing would be to check if there are loose connections at the cryostat or anywhere else. Besides that, try turning the piezo controller off for a few days during e.g. the weekend.
\newline
If the piezo stage is not moving (hitting a wall on the piezo controller screen), it can mean a few things. Either the voltage over the piezo stage is too low and the weight on the stage is too high, or the stage has hit its limit (lower limit ranges per stage), or something is blocking the stage from moving inside the piezo. The first problem can be solved by increasing the voltage, the last one is situational, of course. The piezo should be able to work with lower voltages at CTs. 
\subsection{AWG (R\&S) and Timetagger}
In this case, not a lot of problems can occur. The Timetagger might have trouble initializing if a server connection is used with the internet server option they provide. This can be solved by disconnecting or removing the Timetagger connection from the web application.
\newline
In the case of the AWG, there has only been one problem that has already been discussed in section \ref{AWG_IP}. This solution will re-open the IP connection through a specific port.




Interactive plots----
Download and open in browser to use the interactive plot.
