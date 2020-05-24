---

layout:     post
title:      Implement Black-Scholes Model 
subtitle:   OOP, with C++, Java and Python
date:       2020-05-23
author:     Chauncey
header-img: img/post-bg-isos9-web.jpg
catalog: true
tags:
    - C++
    - Java
    - Python
    - OOP
    - Black-Scholes
---



# Code Structure

- Class: **Option**
  - Fileds: s, k, r, sigma, t
  - Method: d1(), d2()
- derived-class: **CallOption**
  - Method: blackScholesPrice()
- derived-class: **PutOption**
  - Method: blackScholesPrice()

# C++

```c++
#include <iostream>
#include <cmath>


class Option {
protected:
    // Fields
    double s;       //stock price
    double k;       //strike price
    double r;       //continuously compounded risk-free rate
    double sigma;   //volatility of the stock price per year
    double t;       //time to maturity in trading years

public:
    // Constructor
    Option(double s, double k, double r, double sigma, double t) : s(s), k(k), r(r), sigma(sigma), t(t) {}

  	// Method
    // Getter & Setter
    double gets() const {
        return s;
    }

    void sets(double s) {
        Option::s = s;
    }

    double getK() const {
        return k;
    }

    void setK(double k) {
        Option::k = k;
    }

    double getR() const {
        return r;
    }

    void setR(double r) {
        Option::r = r;
    }

    double getSigma() const {
        return sigma;
    }

    void setSigma(double sigma) {
        Option::sigma = sigma;
    }

    double getT() const {
        return t;
    }

    void setT(double t) {
        Option::t = t;
    }

    // Construct Black-Scholes Model
    double time_sqrt() {
        return std::sqrt(t);
    }

    double d1() {
        double d1 = (log(s / k) + r * t) / (sigma * time_sqrt()) + 0.5 * sigma * time_sqrt();
        return d1;
    }

    double d2() {
        double d2 = d1() - sigma * time_sqrt();
        return d2;
    }
		
  	// Cumulative Distribution Function
    double N(const double &z) {
        if (z > 6.0) { return 1.0; }; // this guards against overflow
        if (z < -6.0) { return 0.0; };
        double b1 = 0.31938153;
        double b2 = -0.356563782;
        double b3 = 1.781477937;
        double b4 = -1.821255978;
        double b5 = 1.330274429;
        double p = 0.2316419;
        double c2 = 0.3989423;
        double a = fabs(z);
        double t = 1.0 / (1.0 + a * p);
        double b = c2 * exp((-z) * (z / 2.0));
        double n = ((((b5 * t + b4) * t + b3) * t + b2) * t + b1) * t;
        n = 1.0 - b * n;
        if (z < 0.0) n = 1.0 - n;
        return n;
    };
};

// Derived Class
class CallOption : public Option {
public:
    CallOption(double s, double k, double r, double sigma, double t) : Option(s, k, r, sigma, t) {}

    double blackScholesPrice() {
        double bs = s * N(d1()) - k * exp(-r * t) * N(d2());
        std::cout << "Black-Scholes Price of Call Option: " << bs << std::endl;
        return bs;
    }
};

class PutOption : public Option {

public:
    PutOption(double s, double k, double r, double sigma, double t) : Option(s, k, r, sigma, t) {}
  
    double blackScholesPrice() {
        double bs = k * exp(-r * t) * N(-d2()) - s * N(-d1());
        std::cout << "Black-Scholes Price of Put Option: " << bs << std::endl;
        return bs;
    }
};


int main() {
    CallOption Option1(300, 300, 0.03, 0.15, 1);
    Option1.blackScholesPrice();
    PutOption Option2(300, 300, 0.03, 0.15, 1);
    Option2.blackScholesPrice();
    return 0;
}
```

# Java

```java
import java.lang.Math;

public class bsTest {

	public static void main(String[] args) {
		CallOption Option1 = new CallOption(300, 300, 0.03, 0.15, 1);
		Option1.blackScholesPrice();
		PutOption Option2 = new PutOption(300, 300, 0.03, 0.15, 1);
		Option2.blackScholesPrice();
	}
}

class Option {

	// Fields
	public double s; // stock price
	public double k; // strike price
	public double r; // continuously compounded risk-free rate
	public double sigma; // volatility of the stock price per year
	public double t; // time to maturity in trading years

	// Constructor
	public Option(double s, double k, double r, double sigma, double t) {
		this.s = s;
		this.k = k;
		this.r = r;
		this.sigma = sigma;
		this.t = t;
	}
  
	// Method
	public double getS() {
		return s;
	}

	public void setS(double s) {
		this.s = s;
	}

	public double getK() {
		return k;
	}

	public void setK(double k) {
		this.k = k;
	}

	public double getR() {
		return r;
	}

	public void setR(double r) {
		this.r = r;
	}

	public double getSigma() {
		return sigma;
	}

	public void setSigma(double sigma) {
		this.sigma = sigma;
	}

	public double getT() {
		return t;
	}

	public void setT(double t) {
		this.t = t;
	}

	// Construct Black-Scholes Model
	double time_sqrt() {
		return Math.sqrt(t);
	}

	double d1() {
		double d1 = (Math.log(s / k) + r * t) / (sigma * time_sqrt()) + 0.5 * sigma * time_sqrt();
		return d1;
	}

	double d2() {
		double d2 = d1() - sigma * time_sqrt();
		return d2;
	}
	
	// Cumulative Distribution Function
	double N(double z) {
		if (z > 6.0) {
			return 1.0;
		}
		; // this guards against overflow
		if (z < -6.0) {
			return 0.0;
		}
		;
		double b1 = 0.31938153;
		double b2 = -0.356563782;
		double b3 = 1.781477937;
		double b4 = -1.821255978;
		double b5 = 1.330274429;
		double p = 0.2316419;
		double c2 = 0.3989423;
		double a = Math.abs(z);
		double t = 1.0 / (1.0 + a * p);
		double b = c2 * Math.exp((-z) * (z / 2.0));
		double n = ((((b5 * t + b4) * t + b3) * t + b2) * t + b1) * t;
		n = 1.0 - b * n;
		if (z < 0.0)
			n = 1.0 - n;
		return n;
	};
};

class CallOption extends Option {

	public CallOption(double s, double k, double r, double sigma, double t) {
		super(s, k, r, sigma, t);
	}

	double blackScholesPrice() {
		double bs = s * N(d1()) - k * Math.exp(-r * t) * N(d2());
		System.out.println("Black-Scholes Price of Call Option: " + bs);
		return bs;
	}
};

class PutOption extends Option {

	public PutOption(double s, double k, double r, double sigma, double t) {
		super(s, k, r, sigma, t);
	}

	double blackScholesPrice() {
		double bs = k * Math.exp(-r * t) * N(-d2()) - s * N(-d1());
		System.out.println("Black-Scholes Price of Put Option: " + bs);
		return bs;
	}
};

```

# Python

```python
import numpy as np
import scipy.stats as st


class Option:
    def __init__(self, s, k, r, sigma, t):
        self.s = s
        self.k = k
        self.r = r
        self.sigma = sigma
        self.t = t

    def time_sqrt(self):
        return np.sqrt(self.t)

    def d1(self):
        return (np.log(self.s / self.k) + self.r * self.t) / (
                self.sigma * self.time_sqrt()) + 0.5 * self.sigma * self.time_sqrt()

    def d2(self):
        return self.d1() - self.sigma * self.time_sqrt()


def N(z):
    return st.norm.cdf(z)


class CallOption(Option):
    def __init__(self, s, k, r, sigma, t):
        Option.__init__(self, s, k, r, sigma, t)

    def blackScholesPrice(self):
        bs = self.s * N(self.d1()) - self.k * np.exp(-self.r * self.t) * N(self.d2())
        print("Black-Scholes Price of Call Option: ", bs)
        return bs


class PutOption(Option):
    def __init__(self, s, k, r, sigma, t):
        Option.__init__(self, s, k, r, sigma, t)

    def blackScholesPrice(self):
        bs = self.k * np.exp(-self.r * self.t) * N(-self.d2()) - self.s * N(-self.d1())
        print("Black-Scholes Price of Put Option: ", bs)
        return bs


Option1 = CallOption(300, 300, 0.03, 0.15, 1)
Option2 = PutOption(300, 300, 0.03, 0.15, 1)
Option1.blackScholesPrice();
Option2.blackScholesPrice();

```

