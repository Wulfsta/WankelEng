# WankelEng

This is a SageMath script that draws epitrochoids and their inner envelope. This was written to better understand the 
inner geometry of Wankel Engines.

## Raw:

Here is the raw text, for those not working in a Jupyter notebook:

```sage
n = 2

m = 0.17

t = var('t')

plope = ((1 + m) * cos(t) - m * cos((n + 1) * t), (1 + m) * sin(t) - m * sin((n + 1) * t))

epitrochoid = parametric_plot(plope, (t, -pi, pi), aspect_ratio=1)

show(epitrochoid)

a = var('a')

ite = 7 * (n + 1)
gener = []
for i in xrange(ite):
    rot = i * 2 * pi / ite
    gener.append((cos(rot) * plope[0] - sin(rot) * plope[1] + m * cos(n * -rot) , sin(rot) * plope[0] + cos(rot) * plope[1] + m * sin(n * -rot)))

rays = []

for i in xrange(n + 1):
    rays.append(parametric_plot(((1 + m) * t * cos(i * 2 * pi / (n + 1)), (1 + m) * t * sin(i * 2 * pi / (n + 1))), (t, 0, 1), aspect_ratio=1, color='red'))

show(sum(parametric_plot(gener[i], (t, -pi, pi), aspect_ratio=1) for i in xrange(len(gener))) + sum(rays))

# I should really figure out how to do this without all the sorting.
@parallel('multiprocessing')
def rotor(j):
    choices = []
    # This could be done in parallel as well, it doesn't depend on the order of the loop.
    for i in xrange(ite):
        rot = j + i * 2 * pi / ite
        choices.append( sqrt((gener[i][0].substitute(t == rot))^2 + (gener[i][1].substitute(t == rot))^2) )
    return sorted(choices)[0]

protor = polar_plot(rotor, (t, -pi, pi), aspect_ratio=1)

show(protor)

show('Done!')
```
