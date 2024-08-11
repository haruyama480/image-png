# PNG Decoder/Encoder
original: https://github.com/image-rs/image-png
diff: https://github.com/haruyama480/image-png/compare/master20240811...benchmark-absless-paeth

# benchmark about absless paeth filter

```
rustup install nightly-aarch64-apple-darwin
alias bench="rustup run nightly cargo bench"
bench --bench=unfilter --features=benchmarks,unstable -- --save-baseline my_baseline
# edit src/filter.rs
bench --bench=unfilter --features=benchmarks,unstable -- filter=Sub --baseline my_baseline
```

result
- absless paeth algorithm slows down
```
% bench --bench=unfilter --features=benchmarks,unstable -- filter=Paeth --baseline my_baseline >bench.txt 2>/dev/null 
% cat bench.txt | grep -E '(bpp|p = |Performance)'
unfilter/filter=Paeth/bpp=1
                        time:   [+35.164% +35.745% +36.236%] (p = 0.00 < 0.05)
                        Performance has regressed.
unfilter/filter=Paeth/bpp=2
                        time:   [+39.426% +39.836% +40.326%] (p = 0.00 < 0.05)
                        Performance has regressed.
unfilter/filter=Paeth/bpp=3
                        time:   [-0.7824% -0.4077% -0.0724%] (p = 0.02 < 0.05)
unfilter/filter=Paeth/bpp=4
                        time:   [+30.354% +31.288% +31.943%] (p = 0.00 < 0.05)
                        Performance has regressed.
unfilter/filter=Paeth/bpp=6
                        time:   [+0.0148% +0.2927% +0.5480%] (p = 0.03 < 0.05)
unfilter/filter=Paeth/bpp=8
                        time:   [+33.869% +34.668% +35.280%] (p = 0.00 < 0.05)
                        Performance has regressed.
```

discussion
- this library is optimized to allow SIMD.
- so, more complex can disallow SIMD to slow down.
- https://github.com/image-rs/image-png/pull/382
