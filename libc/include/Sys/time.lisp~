;;; Added 6/26/2013 - Jovan
(in-package #:vacietis.libc.time.h)
(in-readtable vacietis)

(defstruct tm
  tm_sec
  tm_min
  tm_hour
  tm_mday
  tm_mon
  tm_year
  tm_wday
  tm_yday
  tm_isdst)

(defstruct timezone
  tz_minuteswest
  tz_dsttime)

(defstruct timeval
  tv_sec
  tv_usec)

(defun/1 clock ()
  "Returns the CPU time used by the program so far, with type clock_t (usually equivalent to long). The result can be converted to seconds by dividing it by the constant CLOCKS_PER_SEC."
  (get-internal-run-time))

(defun/1 time (pSec)
  "Returns the number of seconds that have elapsed since a certain time (usually January 1, 1970, 00:00:00 o'clock). If the pointer pSec is not NULL, the result is also copied to the location it addresses. The type time_t is generally defined as long."
  (if (not (null pSec)) (setq pSec (get-universal-time)))
  (get-universal-time))

(defun/1 difftime (t1 t0)
  "Returns the number of seconds between t0 and t1."
  (- t1 t0))

(defun/1 gmtime (pSec)
  "Returns a pointer to the current Greenwich Mean Time as a structure of type struct tm, with members of type int for the second, minute, hour, day, etc."
  (multiple-value-bind
	(second minute hour date month year day-of-week dst-p tz)
      (get-decoded-time)
    (setq pSec (make-tm))
    (setf (tm-tm_sec pSec) second)
    (setf (tm-tm_min pSec) minute)
    (setf (tm-tm_hour pSec) hour)
    (setf (tm-tm_mday pSec) date)
    (setf (tm-tm_mon pSec) month)
    (setf (tm-tm_year pSec) year)
    (setf (tm-tm_wday pSec) day-of-week)
    (setf (tm-tm_yday pSec) dst-p)
    (setf (tm-tm_isdst pSec) tz)))

(defun/1 localtime (pSec)
  "Like gmtime(), but returns the local time rather than Greenwich Mean Time."
  (let ((seconds (car (get-decoded-time)))
	(minutes (cadr (get-decoded-time)))
	(
  (defparameter pSec (make-tm)))

(defun/1 ctime (pSec)
  "Generates a string representing the local date and time of the form: Wed Jun 30 21:49:08 1993\n"
)

(defun/1 asctime (ptm)
  "Converts the broken down time value tm into a null-terminated string with the same format as ctime()."
)

    
